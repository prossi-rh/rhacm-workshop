# advanced-cluster-security
Learn how quickly you can get Advanced Cluster Security deployed in your Open Cluster Management installation.

## Deploy the Central Server

Deploy this policy to your HUB to install the Advanced Cluster Security Central Server.

- [Central Server Policy](https://github.com/prossi-rh/rhacm-workshop/blob/master/05.Governance-Risk-Compliance/demo/acs-demo/policy-acs-operator-central.yaml)

## Deploy the Secure Cluster Services

Wait a few minutes for the Central Server to install. Then you will need to setup your command line to have the following CLI and API token.
- `ROX_API_TOKEN` This is a token created in the Central Server to allow the `roxctl` command to make API calls to the Central Server.
- `roxctl` This is the Advanced Cluster Security command line interface.  It's needed to create some certificate bundles that the Secure Cluster Services all need to use.

### Get a token

Follow these steps to get the `ROX_API_TOKEN` for the command line:
1. On your HUB, run `oc get route -n stackrox central`
2. Open a browser to the hostname returned in the `HOST/PORT` column.  Make sure you use `https://hostname` so a secure connection is made. 
3. Run the command `oc get secret -n stackrox central-htpasswd -o yaml | grep password` to view the base64 encoded password.  Decode the password with a command similar to `echo <base64-encoded-password> | base64 -d` to decode the encoded password.
4. Login to the Advanced Cluster Security Central Server with the userid `admin` and the password obtained above.
5. Select `Platform Configuration` and then select `Integrations` from the menu.  Scrol to the end of the list of integrations and select the StackRox API Token integration.
6. Select the New Integration button.  In the dialog that appears, specify a name for the token and select the `admin` role.  Click generate.
7. Copy the generated token and on your command line run a command like `export ROX_API_TOKEN=value` to add the token to your shell environment.

### Download roxctl

Follow these steps to obtain the `roxctl` command:
1. Download from this [link](https://github.com/prossi-rh/rhacm-workshop/blob/master/05.Governance-Risk-Compliance/demo/acs-demo/roxctl) the CLI.
2. Download from this [link](https://github.com/prossi-rh/rhacm-workshop/blob/master/05.Governance-Risk-Compliance/demo/acs-demo/yq).
3. Save the `roxctl` command, copy on the bastion host in /usr/bin and change permission in executable (chmod 755 /usr/bin/roxctl).
4. Save the `yq` command, copy on the bastion host in /usr/bin and change permission in executable (chmod 755 /usr/bin/yq).

### Deploy the certificate bundle

Follow these steps to deploy the certificates that the Advanced Cluster Security Secure Cluster Services will need to connect to the Central Server.
1. Download the script from this repository [deploy-bundle.sh](https://github.com/prossi-rh/rhacm-workshop/blob/master/05.Governance-Risk-Compliance/demo/acs-demo/deploy-bundle.sh)
2. Run the script with the command: `./deploy-bundle.sh -i bundle.yaml | oc apply -f -`

### Deploy the policy

Follow these steps to deploy the Secure Cluster Services policy.  **Note** that this policy requires the steps above to have been completed.
1. On the HUB cluster, deploy the policy [Policy to install the Red Hat Advanced Cluster Security Secure Cluster Services](https://github.com/prossi-rh/rhacm-workshop/blob/master/05.Governance-Risk-Compliance/demo/acs-demo/policy-acs-operator-clusters.yaml)
2. Make sure the policy is set to `enforce` for the `remediationAction`


Note that the default configuration for the bundle and for the policy is to deploy to all managed cluster that are labeled with `vendor=OpenShift`.  This procedure is only intended to work with OpenShift managed clusters.
