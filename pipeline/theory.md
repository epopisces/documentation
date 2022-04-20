
:::mermaid
flowchart TB
  subgraph s1[" "]
    subgraph Bootstrap
      platdef["Platform definition (code)"]
      platids[Platform identities]
      platdeploy[Workstation build process]
    end
    subgraph autplat[Automation Platform]
      pipecode["Core pipeline definition (code)"]
      pipedeps["Core pipeline dependencies (modules)"]
      pipeids[Pipeline integrations]
      pipeinfra[Pipeline compute/build pools, agents]
      subgraph autplattest[Pipeline tests]
        pipevalidation[Automated validation]
      end
    end
    subgraph infrapip[Infrastructure Pipeline]
      iacpipecode["IaC pipeline definition (code)"]
      iacpipedeps["IaC pipeline dependencies (modules)"]
      subgraph iactest[IaC tests]
        tflint
        terratest
      end
    end
    subgraph bldpip[Application Build Pipeline]
      bldpipecode["Build pipeline definition (code)"]
      bldpipedeps["Build pipeline dependencies (modules)"]
      subgraph bldtest[Build tests]
        codescan[Code scanning]
        unittest[Unit testing]
      end
        artifact[Artifact management]
        depmgmt[Dependency management]
      
    end
    subgraph dpypip[Application deploy pipeline]
      dpypipecode["Deploy pipeline definition (code)"]
      dpypipedeps["Deploy pipeline dependencies (modules)"]
      subgraph dpytest[Deploy tests]
        dpyvalidation[Automated validation]
      end
    end
	end

classDef class1 fill: black, stroke: black
class s1 class1
classDef class2 fill: dgrey, stroke: dgrey
class Bootstrap,autplat,infrapip,bldpip,dpypip class2
:::