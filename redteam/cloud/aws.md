# AWS

### Cognito

Identity pools allow to grant users access to AWS Services. We just have to know the id and we will get credentials from an endpoint.

Enumerate permissions associated with cognito credentials with [enumerate-iam](https://github.com/andresriancho/enumerate-iam).

#### Resources

[Internet-Scale Analysis of AWS Cognito Security](https://www.youtube.com/watch?v=_Ek0F-Xh57w)

### Desired State Configuration \(DSC\)

Automates configuration windows servers & clients. Can be written as ps1 and then must then be compiled to .mof files.

### Amazon Systems Manager \(SSM\)

Can apply DSCs to machines. This requires the "Command Document" "AWS-ApplyDSCMofs".

### InSpec 

[Inspec ](https://www.inspec.io/)allows to check deployed labs for correctness.

### Resources

[https://www.mdsec.co.uk/2020/04/designing-the-adversary-simulation-lab/](https://www.mdsec.co.uk/2020/04/designing-the-adversary-simulation-lab/)

