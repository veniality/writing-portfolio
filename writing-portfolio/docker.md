---
title: Running the official Amazon ECR Public or Docker images for the AWS CLI
nav_order: 1
parent: Writing portfolio
---

*[See the live version in the AWS CLI User Guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-docker.html).*

# Running the official Amazon ECR Public or Docker images for the AWS CLI

This topic describes how to run, version control, and configure the AWS CLI version 2 on Docker using either the official Amazon Elastic Container Registry Public (Amazon ECR Public) or Docker Hub image. For more information on how to use Docker, see [Docker's documentation](https://docs.docker.com/).

Official images provide isolation, portability, and security that AWS directly supports and maintains. This enables you to use the AWS CLI version 2 in a container-based environment without having to manage the installation yourself.

{:toc}

**Topics**
* Prerequisites
* Deciding between Amazon ECR Public and Docker Hub
* Run the official AWS CLI version 2 images
* Notes on interfaces and backwards compatibility of the official images
* Use specific versions and tags
* Update to the latest official image
* Share host files, credentials, environment variables, and configuration
* Shorten the docker run command

## Prerequisites

You must have Docker installed. For installation instructions, see the [Docker website](https://docs.docker.com/install/).

To verify your installation of Docker, run the following command and confirm there is an output.

```sh
  $ docker --version
  Docker version 19.03.1
```

## Deciding between Amazon ECR Public and Docker Hub

We recommend using Amazon ECR Public over Docker Hub for AWS CLI images. Docker Hub has stricter rate limiting for public consumers which can cause throttling issues. In addition, Amazon ECR Public replicates images in more than one region to provide strong availability and handle region outage issues.

For more information on Docker Hub rate limiting see [Understanding Docker Hub Rate Limiting](https://www.docker.com/increase-rate-limits/) on the *Docker website*.

## Run the official AWS CLI version 2 images

The first time you use the docker run command, the latest image is downloaded to your computer. Each subsequent use of the docker run command runs from your local copy.

To run the AWS CLI version 2 Docker images, use the docker run command.
<details open markdown="block">
  <summary>
    Amazon ECR Public
  </summary>

  The official AWS CLI version 2 Amazon ECR Public image is hosted on Amazon ECR Public in the aws-cli/aws-cli repository.
  
  
  ```sh
    $ docker run --rm -it public.ecr.aws/aws-cli/aws-cli command
  ```

</details>

<details open markdown="block">
  <summary>
    Docker Hub
  </summary>

  The official AWS CLI version 2 Docker image is hosted on Docker Hub in the amazon/aws-cli repository.
  
  ```sh
    $ docker run --rm -it amazon/aws-cli command
  ```

</details>

This is how the command functions:

* `docker run --rm -it repository/name` – The equivalent of the aws executable. Each time you run this command, Docker spins up a container of your downloaded image, and executes your aws command. By default, the image uses the latest version of the AWS CLI version 2.
  For example, to call the aws --version command in Docker, you run the following.
  <details open markdown="block">
    <summary>
      Amazon ECR Public
    </summary>

    ```sh
      $ docker run --rm -it public.ecr.aws/aws-cli/aws-cli --version
      aws-cli/2.27.41 Python/3.7.3 Linux/4.9.184-linuxkit botocore/2.4.5dev10
    ```

  </details>

  <details open markdown="block">
    <summary>
      Docker Hub
    </summary>
    
    The official AWS CLI version 2 Docker image is hosted on Docker Hub in the amazon/aws-cli repository.
  
    ```sh
      $ docker run --rm -it amazon/aws-cli command
    ```
  </details>

  Amazon ECR Public

  Docker Hub

* `--rm` – Specifies to clean up the container after the command exits.  
* `-it` – Specifies to open a pseudo-TTY with stdin. This enables you to provide input to the AWS CLI version 2 while it's running in a container, for example, by using the aws configure and aws help commands. When choosing whether to omit `-it`, consider the following:  
  * If you are running scripts, `-it` is not needed.  
  * If you are experiencing errors with your scripts, omitting `-it` from your Docker call might fix the issue.  
  * If you are trying to pipe output, `-it` might cause errors and omitting `-it` from your Docker call might resolve this issue. If you'd like to keep the `-it` flag, but still would like to pipe output, disabling the [client-side pager](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-pagination.html#cli-usage-pagination-clientside) the AWS CLI uses by default should resolve the issue.

For more information about the docker run command, see the [Docker reference guide](https://docs.docker.com/engine/reference/run/).



## Notes on interfaces and backwards compatibility of the official images

* The only tool supported on the image is the AWS CLI. Only the `aws` executable should ever be directly run. For example, even though `less` and `groff` are explicitly installed on the image, they should not be executed directly outside of an AWS CLI command.  
* The `/aws` working directory is user controlled. The image will not write to this directory, unless instructed by the user in running an AWS CLI command.  
* There are no backwards compatibility guarantees in relying on the latest tag. To guarantee backwards compatibility, you must pin to a specific `<major.minor.patch>` tag as those tags are immutable; they will only ever be pushed to once.

## Use specific versions and tags

The official AWS CLI version 2 image has multiple versions you can use, starting with version `2.0.6`. To run a specific version of the AWS CLI version 2, append the appropriate tag to your `docker run` command. The first time you use the `docker run` command with a tag, the latest image for that tag is downloaded to your computer. Each subsequent use of the `docker run` command with that tag runs from your local copy.

You can use two types of tags:

* `latest` – Defines the latest version of the AWS CLI version 2 for the image. We recommend you use the `latest` tag when you want the latest version of the AWS CLI version 2\. However, there are no backward-compatibility guarantees when relying on this tag. The `latest` tag is used by default in the `docker run` command. To explicitly use the `latest` tag, append the tag to the container image name.

  <details open markdown="block">
    <summary>
      Amazon ECR Public
    </summary>

    ```sh
      $ docker run --rm -it public.ecr.aws/aws-cli/aws-cli:latest <command>
    ```

  </details>
  <details open markdown="block">
    <summary>
      Docker Hub
    </summary>

    ```sh
      $ docker run \--rm \-it amazon/aws-cli:latest <command>
    ```

  </details>

* `<major.minor.patch>` – Defines a specific version of the AWS CLI version 2 for the image. If you plan to use an official image in production, we recommend you use a specific version of the AWS CLI version 2 to ensure backward compatibility. For example, to run version `2.0.6`, append the version to the container image name.

  <details open markdown="block">
    <summary>
      Amazon ECR Public
    </summary>

    ```sh
      docker run \--rm \-it public.ecr.aws/aws-cli/aws-cli:2.0.6 <command>
    ```

  </details>
  <details open markdown="block">
    <summary>
      Docker Hub
    </summary>

    ```sh
      $ docker run \--rm \-it amazon/aws-cli:2.0.6 <command>
    ```
  </details>

## Update to the latest official image

Because the latest image is downloaded to your computer only the first time you use the `docker run` command, you need to manually pull an updated image. To manually update to the latest version, we recommend you pull the `latest` tagged image. Pulling the image downloads the latest version to your computer.

<details open markdown="block">
    <summary>
        Amazon ECR Public
    </summary>

    ```sh
        docker pull public.ecr.aws/aws-cli/aws-cli:latest
    ```

</details>
<details open markdown="block">
    <summary>
        Docker Hub
    </summary>

    ```sh
        docker pull amazon/aws-cli:latest
    ```
</details>

## Share host files, credentials, environment variables, and configuration

Because the AWS CLI version 2 is run in a container, by default the CLI can't access the host file system, which includes configuration and credentials. To share the host file system, credentials, and configuration to the container, mount the host system’s `~/.aws` directory to the container at `/root/.aws` with the `-v` flag to the `docker run` command. This allows the AWS CLI version 2 running in the container to locate host file information.

<details open markdown="block">
    <summary>
        Amazon ECR Public
    </summary>

    **Linux and macOS**

    ```sh
        docker run --rm -it -v ~/.aws:/root/.aws public.ecr.aws/aws-cli/aws-cli <command>
    ```
    
    **Windows Command Prompt**

    ```sh
        docker run --rm -it -v %userprofile%\.aws:/root/.aws public.ecr.aws/aws-cli/aws-cli <command>
    ```

    **Windows PowerShell**

    ```sh
        docker run --rm -it -v $env:userprofile\.aws:/root/.aws  public.ecr.aws/aws-cli/aws-cli <command>
    ```
    

</details>
<details open markdown="block">
    <summary>
        Docker Hub
    </summary>

    **Linux and macOS**

    ```sh
        docker run --rm -it -v ~/.aws:/root/.aws amazon/aws-cli <command>
    ```
    
    **Windows Command Prompt**

    ```sh
        docker run --rm -it -v %userprofile%\.aws:/root/.aws amazon/aws-cli *command*
    ```

    **Windows PowerShell**

    ```sh
        docker run --rm -it -v $env:userprofile\.aws:/root/.aws amazon/aws-cli <command>
    ```

</details>

For more information about the `-v` flag and mounting, see the [Docker reference guide](https://docs.docker.com/storage/volumes/).

{: .note }
> For information on config and credentials files, see [Configuration and credential file settings in the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html).

### Example 1: Providing credentials and configuration

In this example, we're providing host credentials and configuration when running the `s3 ls` command to list your buckets in Amazon Simple Storage Service (Amazon S3). The below examples use the default location for AWS CLI credentials and configuration files, to use a different location, change the file path.

<details open markdown="block">
    <summary>
        Amazon ECR Public
    </summary>

    **Linux and macOS**

    ```sh
        docker run --rm -it -v ~/.aws:/root/.aws public.ecr.aws/aws-cli/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```
    
    **Windows Command Prompt**

    ```sh
        docker run --rm -it -v %userprofile%\.aws:/root/.aws public.ecr.aws/aws-cli/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```

    **Windows PowerShell**

    ```sh
        docker run --rm -it -v $env:userprofile\.aws:/root/.aws public.ecr.aws/aws-cli/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```
    

</details>
<details open markdown="block">
    <summary>
        Docker Hub
    </summary>

    **Linux and macOS**

    ```sh
        docker run --rm -it -v ~/.aws:/root/.aws amazon/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```
    
    **Windows Command Prompt**

    ```sh
        docker run --rm -it -v %userprofile%\.aws:/root/.aws amazon/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```

    **Windows PowerShell**

    ```sh
        docker run --rm -it -v $env:userprofile\.aws:/root/.aws amazon/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```

</details>

You can call a specific system's environment variables using the `-e` flag. To use an environment variable, call it by name.

<details open markdown="block">
    <summary>
        Amazon ECR Public
    </summary>

    **Linux and macOS**

    ```sh
        docker run --rm -it -v ~/.aws:/root/.aws -e ENVVAR_NAME public.ecr.aws/aws-cli/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```
    
    **Windows Command Prompt**

    ```sh
        docker run --rm -it -v %userprofile%\.aws:/root/.aws -e ENVVAR_NAME public.ecr.aws/aws-cli/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```

    **Windows PowerShell**

    ```sh
        docker run --rm -it -v $env:userprofile\.aws:/root/.aws -e ENVVAR_NAME public.ecr.aws/aws-cli/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```
    

</details>
<details open markdown="block">
    <summary>
        Docker Hub
    </summary>

    **Linux and macOS**

    ```sh
        docker run --rm -it -v ~/.aws:/root/.aws -e ENVVAR_NAME amazon/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```
    
    **Windows Command Prompt**

    ```sh
        docker run --rm -it -v %userprofile%\.aws:/root/.aws -e ENVVAR_NAME amazon/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```

    **Windows PowerShell**

    ```sh
        docker run --rm -it -v $env:userprofile\.aws:/root/.aws amazon/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```

</details>

### Example 2: Downloading an Amazon S3 file to your host system

For some AWS CLI version 2 commands, you can read files from the host system in the container or write files from the container to the host system.

In this example, we download the S3 object s`3://aws-cli-docker-demo/hello` to your local file system by mounting the current working directory to the container's `/aws` directory. By downloading the `hello` object to the container's `/aws` directory, the file is saved to the host system’s current working directory also.

<details open markdown="block">
    <summary>
        Amazon ECR Public
    </summary>

    **Linux and macOS**

    ```sh
        docker run --rm -it -v ~/.aws:/root/.aws -v $(pwd):/aws public.ecr.aws/aws-cli/aws-cli s3 cp s3://aws-cli-docker-demo/hello .
        download: s3://aws-cli-docker-demo/hello to ./hello
    ```
    
    **Windows Command Prompt**

    ```sh
        docker run --rm -it -v %userprofile%\.aws:/root/.aws \-v %cd%:/aws public.ecr.aws/aws-cli/aws-cli s3 cp s3://aws-cli-docker-demo/hello .
    ```

    **Windows PowerShell**

    ```sh
        docker run --rm -it -v $env:userprofile\.aws:/root/.aws \-v $pwd\aws:/aws public.ecr.aws/aws-cli/aws-cli s3 cp s3://aws-cli-docker-demo/hello .
        download: s3://aws-cli-docker-demo/hello to ./hello
    ```
    

</details>
<details open markdown="block">
    <summary>
        Docker Hub
    </summary>

    **Linux and macOS**

    ```sh
        docker run --rm -it -v ~/.aws:/root/.aws -v $(pwd):/aws amazon/aws-cli s3 cp s3://aws-cli-docker-demo/hello .
        download: s3://aws-cli-docker-demo/hello to ./hello
    ```
    
    **Windows Command Prompt**

    ```sh
        docker run --rm -it -v %userprofile%\.aws:/root/.aws -v %cd%:/aws amazon/aws-cli s3 cp s3://aws-cli-docker-demo/hello .
        download: s3://aws-cli-docker-demo/hello to ./hello
    ```

    **Windows PowerShell**

    ```sh
        docker run --rm -it -v $env:userprofile\.aws:/root/.aws -v $pwd\aws:/aws amazon/aws-cli s3 cp s3://aws-cli-docker-demo/hello .
        download: s3://aws-cli-docker-demo/hello to ./hello
    ```

</details>

To confirm the downloaded file exists in the local file system, run the following.

**Linux and macOS**

```sh
    cat hello
    Hello from Docker!
```

**Windows PowerShell**

```sh
    type hello
    Hello from Docker!
```

### Example 3: Using your `AWS_PROFILE` environment variable

You can call a specific system's environment variables using the `-e` flag. Call each environment variable you'd like to use. In this example, we're providing host credentials, configuration, and the `AWS_PROFILE` environment variable when running the `s3 ls` command to list your buckets in Amazon Simple Storage Service (Amazon S3).

<details open markdown="block">
    <summary>
        Amazon ECR Public
    </summary>

    **Linux and macOS**

    ```sh
        docker run --rm -it -v ~/.aws:/root/.aws -e AWS_PROFILE public.ecr.aws/aws-cli/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```
    
    **Windows Command Prompt**

    ```sh
        docker run --rm -it -v %userprofile%\.aws:/root/.aws -e AWS_PROFILE public.ecr.aws/aws-cli/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```

    **Windows PowerShell**

    ```sh
        docker run --rm -it -v $env:userprofile\.aws:/root/.aws -e AWS_PROFILE public.ecr.aws/aws-cli/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```
    

</details>
<details open markdown="block">
    <summary>
        Docker Hub
    </summary>

    **Linux and macOS**

    ```sh
        docker run --rm -it -v ~/.aws:/root/.aws -e AWS_PROFILE amazon/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```
    
    **Windows Command Prompt**

    ```sh
        docker run --rm -it -v %userprofile%\.aws:/root/.aws -e AWS_PROFILE amazon/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```

    **Windows PowerShell**

    ```sh
        docker run --rm -it -v $env:userprofile\.aws:/root/.aws -e AWS_PROFILE amazon/aws-cli s3 ls
        2020-03-25 00:30:48 aws-cli-docker-demo
    ```

</details>


## **Shorten the docker run command**

To shorten the `docker run` command, we suggest you use your operating system's ability to create a [symbolic link](https://www.linux.com/topic/desktop/understanding-linux-links/) (symlink) or [alias](https://www.linux.com/topic/desktop/aliases-diy-shell-commands/) in Linux and macOS, or [doskey](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/doskey) in Windows. To set the `aws` alias, you can run one of the following commands.

For basic access to `aws` commands, run the following.   **Amazon ECR Public**   **Linux and macOS**   $ **alias aws='docker run \--rm \-it public.ecr.aws/aws-cli/aws-cli'**

<details open markdown="block">
    <summary>
        Amazon ECR Public
    </summary>

    **Linux and macOS**

    ```sh
        alias aws='docker run --rm -it public.ecr.aws/aws-cli/aws-cli'
    ```
    
    **Windows Command Prompt**

    ```sh
        doskey aws=docker run --rm -it public.ecr.aws/aws-cli/aws-cli $*
    ```

    **Windows PowerShell**

    ```sh
        Function AWSCLI {docker run --rm -it public.ecr.aws/aws-cli/aws-cli $args}
        Set-Alias -Name aws -Value AWSCLI
    ```
    

</details>
<details open markdown="block">
    <summary>
        Docker Hub
    </summary>

    **Linux and macOS**

    ```sh
        alias aws='docker run --rm -it amazon/aws-cli'
    ```
    
    **Windows Command Prompt**

    ```sh
        doskey aws=docker run --rm -it amazon/aws-cli $*
    ```

    **Windows PowerShell**

    ```sh
        Function AWSCLI {docker run --rm -it amazon/aws-cli $args}
        Set-Alias -Name aws -Value AWSCLI
    ```

</details>

For access to the host file system and configuration settings when using aws commands, run the following.  

<details open markdown="block">
    <summary>
        Amazon ECR Public
    </summary>

    **Linux and macOS**

    ```sh
        alias aws='docker run --rm -it -v ~/.aws:/root/.aws -v $(pwd):/aws public.ecr.aws/aws-cli/aws-cli'
    ```
    
    **Windows Command Prompt**

    ```sh
        doskey aws=docker run --rm -it -v %userprofile%\.aws:/root/.aws -v %cd%:/aws public.ecr.aws/aws-cli/aws-cli $*
    ```

    **Windows PowerShell**

    ```sh
        Function AWSCLI {docker run --rm -it -v $env:userprofile\.aws:/root/.aws -v $pwd\aws:/aws public.ecr.aws/aws-cli/aws-cli $args}
        Set-Alias -Name aws -Value AWSCLI
    ```
    

</details>
<details open markdown="block">
    <summary>
        Docker Hub
    </summary>

    **Linux and macOS**

    ```sh
        alias aws='docker run --rm -it -v ~/.aws:/root/.aws -v $(pwd):/aws amazon/aws-cli'
    ```
    
    **Windows Command Prompt**

    ```sh
        doskey aws=docker run --rm -it -v %userprofile%\.aws:/root/.aws -v %cd%:/aws amazon/aws-cli $*
    ```

    **Windows PowerShell**

    ```sh
        Function AWSCLI {docker run --rm -it -v $env:userprofile\.aws:/root/.aws -v $pwd\aws:/aws amazon/aws-cli $args}
        Set-Alias -Name aws -Value AWSCLI
    ```

</details>
  
To assign a specific version to use in your aws alias, append your version tag.

 <details open markdown="block">
    <summary>
        Amazon ECR Public
    </summary>

    **Linux and macOS**

    ```sh
        alias aws='docker run --rm -it -v ~/.aws:/root/.aws -v $(pwd):/aws public.ecr.aws/aws-cli/aws-cli:2.0.6'
    ```
    
    **Windows Command Prompt**

    ```sh
        doskey aws=docker run --rm -it -v %userprofile%\.aws:/root/.aws -v %cd%:/aws public.ecr.aws/aws-cli/aws-cli:2.0.6 $*
    ```

    **Windows PowerShell**

    ```sh
        Function AWSCLI {docker run --rm -it -v $env:userprofile\.aws:/root/.aws -v $pwd\aws:/aws public.ecr.aws/aws-cli/aws-cli:2.0.6 $args}
        Set-Alias -Name aws -Value AWSCLI
    ```
    

</details>
<details open markdown="block">
    <summary>
        Docker Hub
    </summary>

    **Linux and macOS**

    ```sh
        alias aws='docker run --rm -it -v ~/.aws:/root/.aws -v $(pwd):/aws amazon/aws-cli:2.0.6'
    ```
    
    **Windows Command Prompt**

    ```sh
        doskey aws=docker run --rm -it -v %userprofile%\.aws:/root/.aws -v %cd%:/aws amazon/aws-cli:2.0.6 $*
    ```

    **Windows PowerShell**

    ```sh
        Function AWSCLI {docker run --rm -it -v $env:userprofile\.aws:/root/.aws -v $pwd\aws:/aws amazon/aws-cli:2.0.6 $args}
        Set-Alias -Name aws -Value AWSCLI
    ```

</details>

After setting your alias, you can run the AWS CLI version 2 from within a container as if it's installed on your host system.

```sh
    aws --version
    aws-cli/2.27.41 Python/3.7.3 Linux/4.9.184-linuxkit botocore/2.4.5d
```