reg=nexus-registry-int.apps.tools-na.prod.nextcle.com
releaseimg=/openshift/ocp4:4.6.4-x86_64
export OPENSHIFT_INSTALL_RELEASE_IMAGE_OVERRIDE=nexus-registry-int.apps.tools-na.prod.nextcle.com/openshift/ocp4:4.6.4-x86_64
openshift-install create manifests --dir=.
openshift-install create ignition-configs --dir=.


sudo cp *.ign /var/www/html/openshift4/4.6.4/ignitions/
sudo chmod +r /var/www/html/openshift4/4.6.4/ignitions/*.ign

