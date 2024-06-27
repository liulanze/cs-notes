# Hashicorp Packer
- In the previous time, the traditional way is to deploy code to the server
  first, then to config the environment. One of the cons of this is whenever
  machine number is up to too large, then it will be super hard to maintain and
  patch your machines. By using Packer, we can config the image with required
  configs as well as any needed applications.
- The first traditional way is mutable, while the second way is immutable.
- When we choose the second way to use Packer, when the hosts need to be
  patched, because of the immutable attribute, the hosts need to be killed first
  then to provision new hosts based on the new patched IMAGE. This way, a load
  balancer is necessary due to the hosts which are killed may cause traffic
  loss.
- 3 components of Packer:
  - builders
  - provisioners
  - post-processors
- User variable:
  - You can set a variable in json file, also you can set it when running cli: 
    - packer build -var '<variable_name>=<...>'
  - Same way, you can also create a separate single json file for saving
    variables.  cli:
    - packer build -var -file=<variable.json> <config.json>
- Environment variable
  - Same, that environment variables can be referenced within json file.
- For usage here for cloud provider, authorization is required, role (IAM) can
  be referenced.