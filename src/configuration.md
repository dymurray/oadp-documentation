## Configuration

### Configure Velero Plugins

There are mainly two categories of velero plugins that can be specified while installing Velero:

1. `default-velero-plugins`:<br>
   Five types of default velero plugins can be installed - AWS, GCP, Azure, OpenShift, and CSI. For installation, you need to specify them in the `konveyor.openshift.io_v1alpha1_velero_cr.yaml` file during deployment.
   ```
    apiVersion: konveyor.openshift.io/v1alpha1
    kind: Velero
    metadata:
      name: example-velero
    spec:
      default_velero_plugins:
      - azure
      - gcp
      - aws
      - openshift    
      - csi
   ```
   The above specification will install Velero with all five default plugins. 
  
  Note:
  - For usage of the `csi` plugin with Velero you will need to additionally set `--features=EnableCSI`, please refer the section titled **Usage of Velero `--features` option** for more details. [This](https://github.com/vmware-tanzu/velero-plugin-for-csi/) repository has more information about the CSI plugin.
  - Similarly, for installation of `vsphere` plugin, please refer the section titled **Usage of Velero `--features` option** for more details. [This](https://github.com/vmware-tanzu/velero-plugin-for-vsphere) repository has more information about the vSphere plugin.
   
2. `custom-velero-plugin`:<br>
   For installation of custom velero plugins, you need to specify the plugin `image` and plugin `name` in the `konveyor.openshift.io_v1alpha1_velero_cr.yaml` file during deployment.

   For instance, 
   ```
    apiVersion: konveyor.openshift.io/v1alpha1
    kind: Velero
    metadata:
      name: example-velero
    spec:
      default_velero_plugins:
      - azure
      - gcp
      custom_velero_plugins:
      - name: custom-plugin-example
        image: quay.io/example-repo/custom-velero-plugin   
   ```
   The above specification will install Velero with 3 plugins (azure, gcp and custom-plugin-example).


### Backup Storage Locations and Volume Snapshot Locations Customization

Velero supports backup storage locations and volume snapshot locations from a number of cloud providers (AWS, Azure and GCP). Please refer the section [configure Backup Storage Locations and Volume Snapshot Locations](docs/bsl_and_vsl.md). 

### Using upstream images

In order to use the upstream images for Velero deployment as well as its plugins, you need to set a flag `use_upstream_images` as `true` in the `konveyor.openshift.io_v1alpha1_velero_cr.yaml` during installation of the operator.

<b>Note:</b> If the flag `use_upstream_images` is set, the registry will be switched from `quay.io` to `docker.io` and v1.4.0 (current upstream version) image tag will be used for `Velero` and `latest` image tag will be used for the `plugins`.  

### Resource requests and limits customization

By default, the Velero deployment requests 500m CPU, 128Mi memory and sets a limit of 1000m CPU, 512Mi. Customization of these resource requests and limits may be performed using steps specified in the [Resource requests and limits customization](docs/resource_req_limits.md) section.

### Use self-sigend certificate

If you intend to use Velero with a storage provider that is secured by a self-signed certificate, you may need to instruct Velero to trust that certificate. See [Use self-sigend certificate](docs/self_signed_certs.md) section for details.

### Usage of Velero `--features` option
Some of the new features in Velero are released as beta features behind feature flags which are not enabled by default during the Velero installation. In order to provide `--features` flag values, you need to use the specify the flags under `velero_feature_flags:` in the `konveyor.openshift.io_v1alpha1_velero_cr.yaml` file during deployment.

Some of the usage instances of the `--features` flag are as follows:
- Enabling Velero plugin for CSI: To enable CSI plugin you need to add two things in the `konveyor.openshift.io_v1alpha1_velero_cr.yaml` file during deployment.
  - First, add `csi` under the `default_velero_plugins` 
  - Second, add `EnableCSI` under the `velero_feature_flags`
```
default_velero_plugins:
- csi
velero_feature_flags: EnableCSI
```
- Enabling Velero plugin for vSphere: To enable vSphere plugin you need to do the following things in the `konveyor.openshift.io_v1alpha1_velero_cr.yaml` file during deployment.
  - First, add `vsphere` under the `default_velero_plugins`
  - Second, add `EnableLocalMode` under the `velero_feature_flags`
  - Lastly, add the flag `use_upstream_images` and set it as `true`.
```
default_velero_plugins:
- vsphere
velero_feature_flags: EnableLocalMode
use_upstream_images: true
```
Note: The above is an example of installing the Velero plugin for vSphere in `LocalMode` . Setting `EnableLocalMode` features flag is not always necessary for the usage of vSphere plugin but the pre-requisites must be satisfied and appropriate configuration must be applied, please refer [Velero plugin for vSphere](https://github.com/vmware-tanzu/velero-plugin-for-vsphere) for more details. Also, if you plan on using multiple feature flags at once, pass them to `velero_feature_flags` as comma seperated values, for instance, `velero_feature_flags: EnableLocalMode,EnableCSI`
