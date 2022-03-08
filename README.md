# OpenShift with Red Hat Ceph Storage on Cisco UCS X-Series
Ansible Framework to automate full deployment of Red Hat OpenShift on VMware vSphere with Red Hat OpenShift Data Foundation and Red Hat Ceph Storage as external storage on Cisco UCS X-Series connected to Cisco Intersight.

The complete solution is described in the CVD [LINK]

In this architecture, we have OCP (Red Hat OpenShift) with ODF (Red Hat OpenShift Data Foundation) deployed on Cisco UCS X-Series with Cisco Intersight and Intersight Ansible. In the CVD we automatically have setup seven Cisco UCS X210c M6 blades with Intersight Ansible, simplifying the process of orchestrating a cloud native environment. Three blades were deployed with vSphere 7, running OCP. Four blades were deployed with RHEL 8 and RHCS 4 (Red Hat Ceph Storage), connecting via ODF to OCP as persistent block storage. This can be adjusted by changing the inventory file.

![image](https://github.com/ucs-compute-solutions/UCSX_IMM_OCP_ODF/blob/master/files/pictures/solution_overview.png)

The deployment of the solution is foremost done through Ansible automation. The full deployment of Cisco UCS X-Series is done through Intersight Ansible automation including an installation of vSphere and RHEL with preconfigured kickstart files. It shows the simplicity of the deployment from day 1 and integrates the configuration of vSphere as preparation for OCP and the configuration of RHEL as preparation for Ceph. The further deployment of OCP and Ceph including ODF is done through the OCP installer and the Ceph Ansible installer.

The solution can scale in various directions. The OCP installation can grow in the same X-Series chassis by adding a fourth node and outside of the X-Series chassis by adding more compute power with Cisco UCS X-Series or C-Series. The current OCP configuration can start with the default deployment of three workers but in our environment, we deployed nine workers with more compute power to do a performance benchmark on the whole configuration.

## Solution Flow
The solution setup consists of multiple parts. The high-level flow of the solution setup is as follows:
1.	Deploy Cisco UCS X-Series with Cisco Intersight and Intersight Ansible
2.	Configure vSphere 7 with Ansible and deploy OCP
3.	Configure RHEL 8 with Ansible, deploy RHCS and connect RHCS with ODF

![image](https://https://https://github.com/ucs-compute-solutions/UCSX_IMM_OCP_ODF/blob/master/files/pictures/solution_flow.png)

## Configuration Prerequisites
The solution doesn’t start from day 0 instead requires a few hardware and software configurations, which are listed below:
- The Fabric Interconnects and the required domain policies for both Fabric Interconnects are already configured and de-ployed as well as all VLANs for the solution.
- The connected ports on both Fabric Interconnect are already configured, either as server or as network.
- The Cisco UCS X-Series chassis is already claimed by Intersight and discovered as well as all blades are already discovered.
- The used Intersight Organization in this solution is already created.
- A physical or virtual HTTP server for downloading all required boot images is already configured.
- A physical or virtual RHEL administration host for OCP installation/configuration/administration and Ceph installa-tion/configuration/administration is already configured and runs Ansible.
- A vCenter is already available.
- A shared storage solution is required to be used by the configured vSphere cluster. In our case, we used NetApp shared storage.
- A DHCP and DNS server is already configured.

## Physical Topology
The solution contains one topology configuration. There is one Cisco UCS X-Series chassis connected to a pair of Cisco UCS Fabric Interconnects. The chassis is connected with 8 x 25-Gbps cables from each IO-Module to one Fabric Interconnect. Each Fabric Interconnect has 2 x 100-Gbps cables as uplink to the above Cisco Nexus switches.

Both VM machines for providing HTTP and for OCP installation/configuration/administration and Ceph installa-tion/configuration/administration as well as the shared storage are connected with 25-Gbps cables to the Cisco Nexus switches.

The following diagram illustrates the topology overview.

![image](https://github.com/ucs-compute-solutions/UCSX_IMM_OCP_ODF/blob/master/files/pictures/topology.png)

## Deployment Flow
The deployment of the solution contains various steps. The infrastructure deployment of the Cisco UCS X-Series is based on Intersight Ansible. The configuration of VMware vSphere, the preparation of the Ceph nodes, and the installation of RHCS is based on Ansible as well.

The following information must be modified based on your environment; more information needs to be modified specific to each device automation which is explained later in this document in the device automation sections.
- inventory - contains the variables such as device names and authentication details:
- group_vars/all.yml – contains all information for the solution deployment, update this file based on your environment

The flow of the repository is the following:
1. Deploy the Cisco UCS Infrastructure by running    Setup_UCS_Chassis.yml + Setup_UCS_Server.yml or Setup_UCS.yml
2.	Configure VMware environment by running Setup_VMware.yml
3.	After deploying OCP the next step is to prepare the Ceph nodes by running	Setup_Ceph_Hosts.yml
4.	The final last step is then to deploy Red Hat Ceph Storage and to integrate Ceph into OCP with ODF

Before starting the deployment, Intersight Ansible has to be installed and the API key for accessing Cisco Intersight has to be created.
