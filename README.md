
[![License Badge][License Badge]](https://www.apache.org/licenses/LICENSE-2.0)
[![GitHub Market Place Badge][GitHub Market Place Badge]](https://github.com/marketplace/actions/hashicorp-aws)

HashiCorp AWS - GitHub Action for [Infrastructure as Code][IaC]
---------------------------------------------------------------

[HashiCorp AWS][HashiCorp AWS] is an opinionated CI/CD [GitHub Action][What is GitHub Action] that

1. packages an application into an [AMI image][AWS AMI] and registers it onto AWS, then
2. deploys an [EC2 instance][AWS EC2] of that AMI

![Error loading hashicorp-aws.png](https://github.com/QubitPi/QubitPi/blob/master/img/hashicorp-aws/hashicorp-aws.png?raw=true)

Why
---

I believe infrastructure is the "home" to software. The _highest-quality_ software are not possible unless backed by
the best-made tech infrastructure, which is what I spend most of my management time on in tech area.

### Traditional Software Development

I learned, from years of work experience, that thriving as a Software Engineer with their internal and external 
engagement demands requires high levels of _interpersonal sophistication_. It is no doubt then that the **single 
hardest task in teamwork is _efficient communication_**. This gets exacerbated in a software development cycle in which 
a developer has to distribute their communication efforts among **3** parties, which makes mis-communication frequent

![Error loading traditional.png](https://github.com/QubitPi/QubitPi/blob/master/img/hashicorp-aws/traditional.png?raw=true)

### How Big Techs Improve It

By the time I joined Yahoo at 2016, the company had already made a
[big move by removing all of its QA teams COMPLETELY][Yahoo removed QA]. Software developers were required to write 
automated tests by themselves using open source test frameworks, such as Groovy Spock, Jest, and Cypress. The software 
testing was then fully automated through [Yahoo's own CI platform][Screwdriver], which is developed on top of 
[Jenkins][Jenkins] originally.<br/><br/>This brought a big software quality improvements but in terms of communication 
quality, at least from my experience, was still not optimal. I've had such experience when I finshed implementing a 
system but waited for extra couple of weeks before DevOps Engineer set up a dedicated server for deployment. This 
virtually got mis-translated to my boss as "A software developer had his work delayed for couple weeks".

![Error loading yahoo.png](https://github.com/QubitPi/QubitPi/blob/master/img/hashicorp-aws/yahoo.png?raw=true)

### Software Development Tomorrow (What _HashiCorp AWS_ Does)

The reason we still have DevOps staff is resource isolation using the ubiquitous Docker, which borns out of the
traditional on-premise technology, is _manual_. With on-demand cloud, resource isolations are assumed NOT managed. We 
instead _manage business logics_ with efficiency ([Packer][Packer]) and immutability ([Terraform][Terraform]). **With 
HashiCorp + OpenStack Cloud, business no longer needs ~~Docker + k8s~~**. Teams and developers as well, with almost no 
overhead, are able to eliminate human DevOps<br/><br/>This is the picture from which I'm turning into reality in my 
team. Giving our developer fully **automated** control over infrastructure brings the following benefits to the team:

- **Our developers learn more cutting-edge industry skills compared to their peers which makes our developers more 
  valuable along their career path**
- **Taking hardware constraint into account causes a paradigm shift in how our engineers thought about problems, which 
  makes our developers better skilled and our software better in terms of quality**
- **Eventually reduces the labor costs of company**

[HashiCorp AWS][HashiCorp AWS] does exactly that.

![Error loading new.png](https://github.com/QubitPi/QubitPi/blob/master/img/hashicorp-aws/new.png?raw=true)

How to Use HashiCrop AWS
------------------------

### Step 1 - Setting up HashiCrop Packer & Terraform Template Files

HashiCorp AWS action follows [HashiCrop's best practice][HashiCorp Tutorial] by expecting a directory called 
**hashicorp** located at the root of the repository. It's structure looks like this:

```
.
└── my-app-repository/
    ├── hashicorp/
    │   ├── images/
    │   │   ├── aws-my-app.pkr.hcl
    │   │   └── variables.pkr.hcl
    │   ├── instances/
    │   │   └── main.tf
    │   └── scripts/
    │       └── script.sh
    ├── src
    └── ...
```

> Diagram generated from [tree.nathanfriend.io](https://tree.nathanfriend.io/)

- The **aws-my-app.pkr.hcl** is the standard [HashiCorp Packer Template][HashiCorp Packer Template] file. For example
- The **variables.pkr.hcl**  is the standard [Packer Variable][HashiCorp Packer Variables] definitions
- The **main.tf** is the standard [HashiCorp Terraform Config File][HashiCorp Terraform Config File]
- HashiCorp AWS configures AMI image using
  [Shell Provisioner](https://developer.hashicorp.com/packer/docs/provisioners/shell). This is achieved by placing a 
  **script.sh** file under the `scripts` directory
- A complete working example can be found [here][QubitPi/mlflow hashicorp config files]

### Step 2 - Defining Action File

Under regular `.github/workflows` directory, create a `.yml` file with a arbitrarily preferred name with the following
example contents:

```yaml
---
name: My App IaC Definition (HashiCorp)

"on":
  pull_request:
  push:
    branches:
      - master

jobs:
  hashicorp:
    name: Publish my-app AMI Image and Deploy It to EC2 through HashiCorp
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: hashicorp
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Publish my-app AMI image and deploy it to EC2 through HashiCorp
        uses: QubitPi/hashicorp-aws@master
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}
```

In the example above:

- Each pull request (and every new commits to that PR) validates HashiCorp Packer & Terraform files as tests
- Each `master` branch commit run the tests above and, if successful, publish the image to AWS AMI registry and then 
  deploy the images to EC2 instances automatically
- Old AMI image with the same name will be deregistered. HashiCorp AWS Action takes the opinionated view that
  _deployment should always consume the latest AMI_
- The following [GitHub Action Secrets][How to set up GitHub Action Secrets] needs to be setup

  - [**AWS_ACCESS_KEY_ID**](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html)
  - [**AWS_SECRET_ACCESS_KEY**](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html)
  - [**AWS_REGION**](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html)

- The Security Group of the EC2 instance, however, still needs to be manually configured. This is planned to be
  automated in the near future.
- A complete working example can be found [here][Qubitpi/mlflow github action file]

License
-------

The use and distribution terms for [HashiCorp AWS][HashiCorp AWS] are covered by the
[Apache License, Version 2.0][Apache License, Version 2.0].

<div align="center">
    <a href="https://opensource.org/licenses">
        <img align="center" width="50%" alt="License Illustration" src="https://github.com/QubitPi/QubitPi/blob/master/img/apache-2.png?raw=true">
    </a>
</div>


[Apache License, Version 2.0]: http://www.apache.org/licenses/LICENSE-2.0.html
[AWS AMI]: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html
[AWS EC2]: https://aws.amazon.com/ec2/

[GitHub Market Place Badge]: https://img.shields.io/badge/Github%20Marketplace-1E883D.svg?style=for-the-badge&logo=Github&logoColor=white

[HashiCorp AWS]: https://github.com/marketplace/actions/hashicorp-aws
[HashiCorp Tutorial]: https://developer.hashicorp.com/terraform/tutorials/provision/packer
[HashiCorp Packer Template]: https://developer.hashicorp.com/packer/tutorials/aws-get-started/aws-get-started-build-image#write-packer-template
[HashiCorp Packer Variables]: https://developer.hashicorp.com/packer/tutorials/aws-get-started/aws-get-started-variables
[HashiCorp Terraform Config File]: https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-build#write-configuration
[How to set up GitHub Action Secrets]: https://docs.github.com/en/actions/security-guides/encrypted-secrets

[IaC]: https://qubitpi.github.io/aergia/blog/iac-intro/

[Jenkins]: https://qubitpi.github.io/jenkins.io

[License Badge]: https://img.shields.io/badge/Apache%202.0-F25910.svg?style=for-the-badge&logo=Apache&logoColor=white

[Packer]: https://qubitpi.github.io/hashicorp-packer/packer/docs

[QubitPi/mlflow hashicorp config files]: https://github.com/QubitPi/mlflow/tree/master/hashicorp
[Qubitpi/mlflow github action file]: https://github.com/QubitPi/mlflow/blob/master/.github/workflows/hashicorp.yml

[Screwdriver]: https://screwdriver.cd/

[Terraform]: https://qubitpi.github.io/hashicorp-terraform/terraform/docs

[What is GitHub Action]: https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions

[Yahoo removed QA]: https://spectrum.ieee.org/yahoos-engineers-move-to-coding-without-a-net
