# oidc-provider-github-actions
This repo contains a cloudformation template used to setup the OIDC provider and roles for Github actions. The roles are added via nested stacks. To add more roles simply copy the following section of template.yml and edit the top level indentifier, in this case `roleRepo1`, and `RepositoryName` under `Parameters`.
```yml
  roleRepo1:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: role.yml
      Parameters:
        GitHubOrg: !Ref GitHubOrg
        RepositoryName: my-repo-1
        OIDCProviderArn: !Ref GithubOidc
```
Also copy the `Output` section below and edit as needed...
```yml
roleRepo1:
    Value: !GetAtt roleRepo1.Outputs.Role
    Export:
      Name: !Sub "${AWS::StackName}-roleRepo1"
```

You can then create another stack to define your policies, attaching them to these roles as needed by importing the values exported by this stack. Then use [configure-aws-credentials](https://github.com/aws-actions/configure-aws-credentials) action to get temporary creds in your workflow.

## Deploy
1. Setup your aws cli creds
1. set your aws profile by running `export AWS_PROFILE=<profile>`
1. run `sam build`
1. run `sam deploy --guided`
You will be prompted for inputs names similar to the environment variables above. 

Subsequent deploys to the same stack to the default environment...
1. run `sam build`
1. run `sam deploy`
