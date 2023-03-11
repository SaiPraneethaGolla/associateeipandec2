# associateeipandec2
AWSTemplateFormatVersion: '2010-09-09'
Description: Provision a simple Amazon Linux Instance and allow SSH connectivity.
Parameters:
  KeyPairName:
    Description: EC2 Key Pair for SSH Access, you must have created these prior to
      running this.
    Type: AWS::EC2::KeyPair::KeyName
    Default: batch11_keypair
  Batch11VpcId:
    Description: 'VPC for creating instance using CFT'
    Type: AWS::EC2::VPC::Id
    Default: vpc-03c09d2538cafa97a

  AmiId:
    Description: 'Using Amazon Linux Image for creating this Instance'
    Type: String
    Default: ami-09ba48996007c8b50

  InstanceType:
    Description: 'Instance type configuration'
    Type: String
    Default: t2.micro

  SubnetId:
    Description: 'Giving the public-subnet1 for this instance'
    Type: AWS::EC2::Subnet::Id
    Default: subnet-06dd1f259fe362a7d 
  
  InstanceId:
    Description: Instance to which EIP to be allocated.
    Type: String
    Default: i-0e51217e5738a8cf8
  AllocationId:
    Description: Allocation ID for EIP
    Type: String
    Default: eipalloc-04e80eaea20a54a9f
  PrivateIp:
    Description: Private IP Address of EC2 Instance
    Type: String
    Default: 172.31.38.149

Resources:
  Batch11SecurityGroup:
      Type: AWS::EC2::SecurityGroup
      Properties: 
        GroupDescription: Batch11 Security Group for test purpose
        GroupName: Batch11
        SecurityGroupIngress: 
          - IpProtocol: tcp
            FromPort: 22
            ToPort: 22
            CidrIp: 0.0.0.0/0
          - IpProtocol: tcp
            FromPort: 80
            ToPort: 80
            CidrIp: 0.0.0.0/0   
        Tags:
          - Key: "Name"
            Value: "Batch11"
        VpcId: !Ref Batch11VpcId
         
  NaveenInstance:
      Type: AWS::EC2::Instance
      Properties:
        KeyName: !Ref KeyPairName
        ImageId: !Ref AmiId
        InstanceType: !Ref InstanceType
        SecurityGroupIds: 
          - !Ref Batch11SecurityGroup
        # NetworkInterfaces:
        #   - GroupSet: !Ref Batch11SecurityGroup
        #     SubnetId: !Ref SubnetId
        #     AssociatePublicIpAddess: true
        #      DeviceIndex: 0
        #      DeleteOnTermination: true
        Tags:
          - Key: "Name"
            Value: "Naveen Instance" 

  Batch11ElasticIP:
      Type: AWS::EC2::EIP
      Properties: 
        # Domain: String
        # InstanceId: String
        # NetworkBorderGroup: String
        # PublicIpv4Pool: String
        Tags: 
          - Key: "Name"
            Value: "Batch11 EIP" 
        # TransferAddress: String

  EipAssociate:
    Type: AWS::EC2::EIPAssociation
    Properties: 
      EIP: !Ref Batch11ElasticIP
      InstanceId: !Ref NaveenInstance
