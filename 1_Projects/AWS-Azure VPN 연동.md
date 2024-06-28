#vpn #site-to-site #vn 

> [!note] 
> AWS의 VPC 주소 공간과 Azure의 Virtual Network 주소 공간은 서로 달라야 한다. 
> 예를 들어, a-vpc(aws)은 10.0.0.0/16 이라면 b-vn(Azure)는 10.1.0.0/16 이어야 한다.
> 왜냐하면 VPN을 통한다면 인터넷을 통하지 않는 같은 네트워크로 동작하기 때문이다.
> 
> AWS에서 미리 VPC를 만들어놨다면 VPC 상세 정보에서 CIDR 정보를 확인하면 된다.


### Step-by-Step Guide: Connecting Azure and AWS with a VPN

#### **Part 1: Setting Up the Azure Side**

1. **Create a Virtual Network in Azure**
    
    - Go to the Azure portal.
    - Navigate to "Create a resource" > "Networking" > "Virtual network".
    - Fill in the details:
        - Name: Choose a name for your VNet.
        - Address space: Specify the address space (e.g., 10.1.0.0/16).
        - Subnets: Add at least one subnet (e.g., 10.1.1.0/24).
    - Click "Create".
2. **Create a Virtual Network Gateway**
    
    - In the Azure portal, navigate to "Create a resource" > "Networking" > "Virtual network gateway".
    - Fill in the details:
        - Name: Choose a name for your gateway.
        - Gateway type: VPN.
        - VPN type: Route-based.
        - SKU: Select the appropriate SKU (e.g., VpnGw1).
        - Virtual network: Select the VNet created in step 1.
        - Public IP address: Create a new public IP address.
    - Click "Create".
3. **Create a Local Network Gateway**
    
    - In the Azure portal, navigate to "Create a resource" > "Networking" > "Local network gateway".
    - Fill in the details:
        - Name: Choose a name for your local network gateway.
        - IP address: Enter the public IP address of your AWS VPN endpoint (to be obtained in AWS setup).
        - Address space: Enter the address space of your AWS VPC (e.g., 10.0.0.0/16).
    - Click "Create".

#### **Part 2: Setting Up the AWS Side**

1. **Create a Virtual Private Gateway**
    
    - Go to the AWS Management Console.
    - Navigate to "VPC" > "Virtual Private Gateways".
    - Click "Create Virtual Private Gateway".
    - Fill in the details:
        - Name tag: Choose a name for your gateway.
        - ASN: Leave default or specify a custom ASN if required.
    - Click "Create".
    - After creation, select the Virtual Private Gateway and click "Actions" > "Attach to VPC". Select your VPC and attach the gateway.
2. **Create a Customer Gateway**
    
    - In the AWS Management Console, navigate to "VPC" > "Customer Gateways".
    - Click "Create Customer Gateway".
    - Fill in the details:
        - Name tag: Choose a name for your customer gateway.
        - IP Address: Enter the public IP address of your Azure Virtual Network Gateway (obtained from Azure setup).
        - BGP ASN: Enter the BGP ASN if you are using BGP, otherwise leave it blank.
    - Click "Create".
3. **Create a Site-to-Site VPN Connection**
    
    - In the AWS Management Console, navigate to "VPC" > "VPN Connections".
    - Click "Create VPN Connection".
    - Fill in the details:
        - Name tag: Choose a name for your VPN connection.
        - Target gateway type: Virtual Private Gateway.
        - Virtual Private Gateway: Select the one you created.
        - Customer Gateway: Select the one you created.
        - Routing options: Static (if you are not using BGP) or Dynamic (if using BGP).
        - Static IP Prefixes: Enter the Azure VNet address space (e.g., 10.1.0.0/16) if using static routing.
    - Click "Create".
    - After creation, select the VPN connection and download the configuration file.

#### **Part 3: Configure VPN Connection on Azure Side**

1. **Create a Connection**
    
    - In the Azure portal, navigate to your Virtual Network Gateway.
    - Click "Connections" > "Add".
    - Fill in the details:
        - Name: Choose a name for your connection.
        - Connection type: Site-to-site (IPsec).
        - Virtual network gateway: Select your Azure Virtual Network Gateway.
        - Local network gateway: Select your Local Network Gateway.
        - Shared key (PSK): Use the pre-shared key from the AWS VPN configuration file.
    - Click "OK".
2. **Configure the Azure VPN Gateway**
    
    - Ensure the Azure Virtual Network Gateway is configured to use the correct settings from the AWS VPN configuration file, including IKE and IPsec policies.

#### **Part 4: Configure Routing and Security Groups**

1. **Update Route Tables in AWS**
    
    - Navigate to "VPC" > "Route Tables".
    - Select the route table associated with your VPC.
    - Add a route:
        - Destination: Azure VNet address space (e.g., 10.1.0.0/16).
        - Target: Virtual Private Gateway.
2. **Update Network Security Groups in AWS and Azure**
    
    - Ensure that the security groups in AWS allow inbound and outbound traffic for the necessary ports and protocols to and from the Azure address space.
    - Similarly, update the network security groups in Azure to allow traffic to and from the AWS VPC address space.
3. **Test the Connection**
    
    - Once the VPN connection is configured on both sides, test the connectivity by pinging resources across the VPN from your Azure instance to AWS and vice versa.

By following these detailed steps, you should be able to establish a secure VPN connection between your Azure Windows 11 Pro instance and your AWS ElastiCache instance.