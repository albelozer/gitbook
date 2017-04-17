# Amazon EC2 SSH connection

## To access your instance

1. Open an SSH client.
2. Locate your private key file (ansible-test.pem). The wizard automatically detects the key you used to launch the instance.
3. Your key must not be publicly viewable for SSH to work. Use this command if needed:

    ```sh
    chmod 400 ansible-test.pem
    ```

4. Connect to your instance using its Public DNS:

    ```sh
    ec2-35-166-112-128.us-west-2.compute.amazonaws.com
    ```

> **Note** You may need to change the settings of *security group* at AWS to grant SSH access from your IP.

## Example

```sh
ssh -i "ansible-test.pem" ec2-user@ec2-35-166-112-128.us-west-2.compute.amazonaws.com
```

Please note that in most cases the username above will be correct, however please ensure that you read your AMI usage instructions to ensure that the AMI owner has not changed the default AMI username.
If you need any assistance connecting to your instance, please see our connection documentation.
