# Cloud Formation

## I have build the Whole VPC Infrastructure using Cloud Formation. 
## Infrastructure As A Code

```yml
Parameters:
  AMI: 
    Type: String
    Description: Custom AMI
    Default: ami-09246ddb00c7c4fef
  InstanceType:
    Type: String
    Description: Custom Instance Type
    Default: t2.micro
  KeyValuePair:
    Type: String
    Description: Enter your keyname
    Default: sanketfinal
​
Resources:
  myVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: '10.0.0.0/16'
      EnableDnsHostnames: true
      EnableDnsSupport: true
      InstanceTenancy: default
      Tags: 
        - Key: Name
          Value: My VPC Network
​
  myIG:
    Type: AWS::EC2::InternetGateway
    Properties: 
      Tags: 
        - Key: Name
          Value: My Internet Gateway
​
  myIGAttachment:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId:
        Ref: myVPC
      InternetGatewayId:
        Ref: myIG
​
  mySubnet:
    Type: AWS::EC2::Subnet
    Properties: 
      VpcId:
        Ref: myVPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone: !Select [ 0, !GetAZs ]
      Tags: 
      - Key: Name
        Value: My Subnet Lab
​
  myRT:
    Type: AWS::EC2::RouteTable
    Properties: 
      VpcId:
        Ref: myVPC
      Tags: 
      - Key: Name
        Value: My Routing Table
​
  myRoute:
    Type: AWS::EC2::Route
    DependsOn: myIG
    Properties:
      RouteTableId:
        Ref: myRT
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId:
        Ref: myIG
​
  mySubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId:
        Ref: mySubnet
      RouteTableId:
        Ref: myRT
​
  MyEC2Instance: 
    Type: AWS::EC2::Instance
    Properties: 
      ImageId: !Ref AMI
      KeyName: !Ref KeyValuePair
      InstanceType: !Ref InstanceType
      SecurityGroupIds:
      - Ref: mySecurityGroup
      SubnetId: !Ref mySubnet
      Tags: 
      - Key: Name
        Value: OS using CloudFormation
      - Key: Author
        Value: Sanket Badjate
​
  myEIP:
    Type: AWS::EC2::EIP
    Properties:
      InstanceId: !Ref MyEC2Instance
​
  mySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow http to client host
      VpcId:
        Ref: myVPC
      SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: 80
        ToPort: 80
        CidrIp: 0.0.0.0/0
      SecurityGroupEgress:
      - IpProtocol: -1
        CidrIp: 0.0.0.0/0
      Tags:
      - Key: Name
        Value: My Security Group
```
