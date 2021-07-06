# api

**Table of Contents** _generated with_ [_DocToc_](https://github.com/thlorenz/doctoc)

* [execute Groovy script with an API call](api.md#execute-groovy-script-with-an-api-call)
* [stop build via api](api.md#stop-build-via-api)
* [get builds information](api.md#get-builds-information)
* [list plugins](api.md#list-plugins)
  * [using api \(`curl`\)](api.md#using-api-curl)
  * [using cli](api.md#using-cli)
* [builds](api.md#builds)
  * [get particular build parameters](api.md#get-particular-build-parameters)
  * [get all parameters via Json format](api.md#get-all-parameters-via-json-format)

> reference:
>
> * [Example for Jenkins API](https://www.programcreek.com/java-api-examples/?action=search&ClassName=jenkins&submit=Search)
> * [Remote access API](https://wiki.jenkins.io/display/JENKINS/Remote+access+API/)
> * [How to build a job using the REST API and cURL?](https://support.cloudbees.com/hc/en-us/articles/218889337-How-to-build-a-job-using-the-REST-API-and-cURL-?page=64)
> * [7 useful Jenkins Rest services](https://www.esentri.com/7-useful-jenkins-rest-services/)

## [execute Groovy script with an API call](https://support.cloudbees.com/hc/en-us/articles/217509228-Execute-Groovy-script-in-Jenkins-with-an-API-call)

```bash
$ curl -d "script=$(cat /tmp/script.groovy)" -v --user username:ApiToken http://JENKINS_URL/scriptText

# or
$ curl -d "script=println 'this script works'" -v --user username:ApiToken http://JENKINS_URL/scriptText
```

## [stop build via api](https://www.jenkins.io/doc/book/using/aborting-a-build/)

| api | comments |
| :---: | :--- |
| `BUILD_URL/stop` | abort a build |
| `BUILD_URL/term` | forcibly terminate a build |
| `BUILD_URL/kill` | hard kill a pipeline |

## get builds information

> reference:
>
> * [USING JENKINS / HUDSON REMOTE API TO CHECK JOBS STATUS](http://blog.dahanne.net/2014/04/02/using-jenkins-hudson-remote-api-to-check-jobs-status/)
> * [justlaputa/jenkins-api.md](https://gist.github.com/justlaputa/5634984)

* [via job api](https://stackoverflow.com/a/25650246/2940319)

  ```bash
  $ curl -sSLg http://jenkins:8080/job/my-job/api/json?tree=builds[id,number,duration,timestamp,builtOn]
  ```

* get particular fields for all builds

  > api format: `api/json?tree=allBuilds[Bartifact,description,building,displayName,duration,estimatedDuration,fullDisplayName,id,number,queueId,result,timestamp,url]`

  ```bash
  $ curl -s --globoff 'https://<JENKINS_DOMAIN_NAME>/job/<jobname>/api/json?tree=allBuilds[artifact,description,building,displayName,duration,estimatedDuration,fullDisplayName,id,number,queueId,result,timestamp,url]' | jq --raw-output .
  ```

## list plugins

### [using api \(`curl`\)](https://stackoverflow.com/a/52836951/2940319)

```bash
$ curl -u<username>:<password> \
    -s https://<JENKINS_DOMAIN_NAME>/pluginManager/api/json?depth=1 \
    | jq -r '.plugins[] | "\(.shortName):\(.version)"' \
    | sort
```

* [or](https://stackoverflow.com/a/17241066/2940319)

  ```bash
  $ curl -s 'https://<JENKINS_DOMAIN_NAME>/pluginManager/api/json?pretty=1&tree=plugins\[shortName,longName,version\]'
  {
    "_class": "hudson.LocalPluginManager",
    "plugins": [
      {
        "longName": "SSH Credentials Plugin",
        "shortName": "ssh-credentials",
        "version": "1.18.1"
      },
      {
        "longName": "Configuration as Code Plugin",
        "shortName": "configuration-as-code",
        "version": "1.47"
      },
      ...
  }
  ```

### [using cli](https://stackoverflow.com/a/44979051/2940319)

```bash
$ cat plugin.groovy
def plugins = jenkins.model.Jenkins.instance.getPluginManager().getPlugins()
plugins.each {println "${it.getShortName()}: ${it.getVersion()}"}
```

* by `jar`

  ```bash
  $ curl -fsSL -O https://JENKINS_URL/jnlpJars/jenkins-cli.jar
  $ java -jar jenkins-cli.jar \
    [-auth <username>:<password>] \
    -s https://JENKINS_URL groovy = < plugin.groovy
  ```

  * or

    ```bash
    $ java -jar jenkins-cli.jar \
      [-auth <username>:<password>] \
      -s https://JENKINS_URL \
      list-plugins
    ```

* [by `ssh`](https://www.jenkins.io/doc/book/managing/cli/)

  ```bash
  $ ssh [-i <private-key>] [-l <user>] -p <port> JENKINS_URL groovy =< plugin.groovy
  ```

  * or

    ```bash
    $ ssh [-i <private-key>] [-l <user>] -p <port> JENKINS_URL list-plugins
    ```

  * or

    ```bash
    $ ssh [-i <private-key>] [-l <user>] -p <port> JENKINS_URL groovy < = <script.groovy>
    ```

## builds

### get particular build parameters

```bash
$ curl -s https://<JENKINS_DOMAIN_NAME>/job/<jobname>/<buildnum>/api/xml?xpath=/workflowRun/action/parameter[name="<param_name>"]/value
```

* remove xml tag

  ```bash
  $ curl -s 'https://<JENKINS_DOMAIN_NAME>/job/<jobname>/<buildnum>/api/xml?xpath=/workflowRun/action/parameter\[name="tester"\]/value' |
         sed -re 's:<[^>]+>([^<]+)<.*$:\1:'
  ```

* i.e.:

  ```bash
  $ curl -s --globoff 'https://<JENKINS_DOMAIN_NAME>/job/<jobname>/<buildnum>/api/xml?xpath=/*/action/parameter[name=%22id%22]'
  <parameter _class="hudson.model.StringParameterValue"><name>id</name><value>marslo</value></parameter>

  $ curl -s --globoff 'https://<JENKINS_DOMAIN_NAME>/job/<jobname>/<buildnum>/api/xml?xpath=/*/action/parameter[name=%22id%22]/value'
  <value>marslo</value>

  $ curl -s --globoff 'https://<JENKINS_DOMAIN_NAME>/job/<jobname>/<buildnum>/api/xml?xpath=/*/action/parameter[name=%22id%22]/value' |
         sed -re 's:<[^>]+>([^<]+)<.*$:\1:'
  marslo
  ```

### get all parameters via Json format

> api: `https://<JENKINS_DOMAIN_NAME>/job/<jobname>/<buildnum>/api/json?tree=actions[parameters[*]]`

```bash
$ curl -s --globoff 'https://<JENKINS_DOMAIN_NAME>/job/<jobname>/<buildnum>/api/json?tree=actions[parameters[*]]' | jq --raw-output '.actions[].parameters[]?'
{
  "_class": "hudson.model.StringParameterValue",
  "name": "id",
  "value": "marslo"
}
{
  "_class": "hudson.model.StringParameterValue",
  "name": "gender",
  "value": "female"
}
```

* [additional format](../../good/character/json.md)

  ```bash
  $ curl -s --globoff 'https://<JENKINS_DOMAIN_NAME>/job/<jobname>/<buildnum>/api/json?tree=actions[parameters[*]]' | jq --raw-output '.actions[].parameters[]? | .name + "\t" + .value'
  id    marslo
  gender    female
  ```

> **jq tips**
>
> * [remove empty line from output](https://stackoverflow.com/a/44289083/2940319)
>
>   i.e.:
>
>   * original `jq --raw-output .actions[].parameters`
>   * remove empty line: `jq --raw-output '[.actions[].parameters | select(length > 0) ]'`

```bash
$ curl -s --globoff 'https://<JENKINS_DOMAIN_NAME>/job/<jobname>/<buildnum>/api/json?tree=actions[parameters[*]]' | jq --raw-output '[.actions[].parameters | select(length > 0)]'
[
  [
    {
      "_class": "hudson.model.StringParameterValue",
      "name": "id",
      "value": "marslo"
    },
    {
      "_class": "hudson.model.StringParameterValue",
      "name": "gender",
      "value": "female"
    }
  ]
]
```
