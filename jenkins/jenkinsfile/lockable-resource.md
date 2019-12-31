
# Lockable Resource Manager

## get info

### get all labels
```groovy
import org.jenkins.plugins.lockableresources.LockableResourcesManager;

stage('all label') {
    println '~~> all labels:'
    println new LockableResourcesManager().getAllLabels()
}
```

### Get resource by label
```groovy
import org.jenkins.plugins.lockableresources.LockableResourcesManager;

stage('get label') {
    String l = 'my-label'
    println "~~> resources for ${l}:"
    println new LockableResourcesManager().getResourcesWithLabel(l, null)
}
```


### is label valided
```groovy
import org.jenkins.plugins.lockableresources.LockableResourcesManager;

stage('is label valid') {
    String l = 'my-label'
    println '~~> is ${l} valid:'
    println new LockableResourcesManager().isValidLabel(l)
}
```

### Get free number of label
```groovy
import org.jenkins.plugins.lockableresources.LockableResourcesManager;

stage('number of free') {
    String l = 'my-label'
    println new LockableResourcesManager().getFreeResourceAmount(l)
}
```


### [Get all resource](https://issues.jenkins-ci.org/browse/JENKINS-46235?focusedCommentId=345401&page=com.atlassian.jira.plugin.system.issuetabpanels%3Acomment-tabpanel#comment-345401)
```groovy
stage('get all resoruces') {
    def all_lockable_resources = GlobalConfiguration.all().get(org.jenkins.plugins.lockableresources.LockableResourcesManager.class).resources

    println "~~> free resource for ${l}"
    println all_lockable_resources
    // remove
    all_lockable_resources.removeAll { it.name.contains('somestr')}
}
```

## add or remove
### [remove by label (or name)](https://issues.jenkins-ci.org/browse/JENKINS-38906?focusedCommentId=353245&page=com.atlassian.jira.plugin.system.issuetabpanels%3Acomment-tabpanel#comment-353245)

```groovy
stage('remove') {
    def manager = org.jenkins.plugins.lockableresources.LockableResourcesManager.get()
    def resources = manager.getResources().findAll {
        // println it.locked ? "${it} locked" : "${it.labels}"
        ( !it.locked ) && (
            it.name.equals('marslo') ||
            it.labels.contains('marslo') ||
            it.name.startsWith('marslo')
        )
    }
    currentBuild.description = "${resources.size()} locks"
    resources.each {
        println "Removing ${it.name} ~~> ${it.labels}"
        manager.getResources().remove(it)
    }
    manager.save()
}
```

### create new item
```groovy
stage('create') {
    def manager = org.jenkins.plugins.lockableresources.LockableResourcesManager.get()
    def myr = manager.createResourceWithLabel('marslo', 'marslo-label')
}
```


## reserve & unlock
### by cli
```bash
$ resource='marslo'
$ curl -XGET -uadmin:passwd https://my.jenkins.com/lockable-resources/reserve?resource=${resource}
$ curl -XGET -uadmin:passwd https://my.jenkins.com/lockable-resources/unreserve?resource=${resource}
```

### by api
```groovy
stage('reserve & unlock') {
    def manager = org.jenkins.plugins.lockableresources.LockableResourcesManager.get()
    println manager.fromName('marslo')?.isReserved()

    println '~~> lock marslo:'
    manager.reserve([ manager.fromName('marslo') ], 'Marslo Jiao')
    println manager.fromName('marslo')?.isReserved()

    println '~~> unlock marslo:'
    manager.reset([ manager.fromName('marslo') ])
    println manager.fromName('marslo')?.isReserved()

}

```

### [examples](https://config9.com/apps/jenkins/jenkins-lockable-resource-lock-without-unlocking/)
```groovy
print "START\n"
def all_lockable_resources = org.jenkins.plugins.lockableresources.LockableResourcesManager.get().resources
all_lockable_resources.each { r->
    if (r.isLocked() || r.isReserved()) {
    println "Lock " + r + " is locked or reserved by " + r.getBuild() + " B CARSE " + r.getLockCause()

    b = r.getBuild()

        if (b) {
            if (b.isBuilding()) { println "build:" + b + " is building" }
            if (b.getResult().equals(null)) { println "build:" + b + " result is not in yet" }

            if ( ! b.isBuilding() && ! b.getResult().equals(null)) {
                println "build:" + b + " is not building and result is " + b.getResult() + " yet the lock " + r + " is locked."
                println "ACTION RELEASE LOCK " + r

                println "getLockCause:" + r.getLockCause()
                println "getDescription:" + r.getDescription()
                println "getReservedBy:" + r.getReservedBy()
                println "isReserved:" + r.isReserved()
                println "isLocked:" + r.isLocked()
                println "isQueued:" + r.isQueued()

                //release the lock
                r.reset()

                println "getLockCause:" + r.getLockCause()
                println "getDescription:" + r.getDescription()
                println "getReservedBy:" + r.getReservedBy()
                println "isReserved:" + r.isReserved()
                println "isLocked:" + r.isLocked()
                println "isQueued:" + r.isQueued()

            }
        }

    }
}
```