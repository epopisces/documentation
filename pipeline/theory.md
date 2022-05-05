
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

# Bootstrap

## Role
* Deploy pipeline from scratch, solving chicken-and-egg

# Automation Pipeline

## Role
* single endpoint for automation
  * 'routes' to other pipeline steps as needed
* handles caching
* handles pipeline compute (build pools, etc)
* handles core naming convention/associations (eg for a given project with all elements involved, creates consistency across infra, app artifact, deployment, etc)
* provides common modules for universal integrations, eg documentation output
* when changes are made to pipeline, includes testing (via series of fast builds to validate functioning of pipeline in variety of use cases)

# Infrastructure Pipeline
aka IaC

## Role
Extends Automation Pipeline, adding the following capabilities
* steps to provision infrastructure in Azure

# App Build Pipeline

## Role
Extends Automation Pipeline, adding the following capabilities
* unit testing
* code scanning
* artifact management
* dependency management

# App Deploy Pipeline

## Role
* s