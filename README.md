gradle-provisioning-example
===========================

To start provisioning you need to first start virtualbox webservice (i faced an issue on Ubuntu described here http://ubuntuforums.org/showthread.php?t=2196096)

```
vboxwebsrv -H ::1
```

Then type
```
gradlew provision
```

This will fire image build on vbox, provisioning and upload to amazon AWS.

For AWS you need to specify your Amazon accessKeyId and accessSecret and setup AWS tools, see how to do it:
https://github.com/danveloper/provisioning-gradle-plugin#amazon-aws 

Known issues
-------------

When provisioning plugin tries to download base image from "http://mirror.ancl.hawaii.edu/linux/centos/6.5/isos/x86_64/CentOS-6.5-x86_64-netinstall.iso" I faced an issue.
The download failed.
A workaround can be to download the image manually, rename it to 'install.iso' and put in project directory build/provisioning-cache

Also connecting to local vboxwebsrv instance with http://localhost:18083 did not work so I had to change it to IPv6 host name from /etc/hosts
This one worked fine : http://ip6-localhost:18083

About Virtual Box SOAP API
----------------------------
When running the vboxwebsrv you can get to the WSDL of the service at 
http://ip6-localhost:18083/vboxwebService.wsdl
Virtal Box SDK reference is at http://download.virtualbox.org/virtualbox/SDKRef.pdf
This is a simple groovy SOAP client which lists local virtual images:
```
@GrabResolver(name='clojars.org', root='http://clojars.org/repo')
@Grab('org.clojars.tbatchelli:vboxjws:4.3.4')
import org.virtualbox_4_3.*
 
// Setup Instructions
// ------------------
//
// Getting setup is a bit of a PITA. I recommend disabling auth for testing purposes. Also in a
// a real development environment I would recommend pulling the vboxjws.jar from VirtualBox
// rather than using the version published on Clojars... why VirtualBox does not publish more
// recent versions of vboxjws.jar to Maven is a mystery to me.
//
// 1. disable virtualbox authentication with the following command:
//     
//     $ VBoxManage setproperty websrvauthlibrary null
// 
// 2. start the SOAP server
//
//    $ vboxwebsrv
//
// 3. Go!
//
 
def url="http://ip6-localhost:18083"
def username = null
def password = null
 
def vboxManager = VirtualBoxManager.createInstance(null)
vboxManager.connect(url, username, password)
 
def vbox = vboxManager.vbox
 
println "VirtualBox version: ${vbox.version}"
 
println "--- [Machines] ---"
vbox.machines.each {
    println it.name
}
```
