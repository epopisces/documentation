:::mermaid
flowchart TB
  Input
  subgraph s1[" "]
    Input --> Process
    subgraph s2 [" "]
      subgraph Process
        direction LR
        A --> B --> C --> A
      end
      Process --> Output
    end
	end
  Output --> Input

classDef class1 fill: black, stroke: black
class s1,s2 class1
style Process fill: dgrey, stroke: dgrey
:::

:::mermaid
flowchart TB
  subgraph s1[" "]
    subgraph Bootstrap
      platdef["Platform Definition (code)"]
      platids[Platform Identities]
      platdeploy[Workstation Build Process]
    end
    subgraph autplat[Automation Platform]
      pipecode["Core Pipeline Definition (code)"]
      pipedeps["Core Pipeline Dependencies (modules)"]
      pipeids[Pipeline Integrations]
      pipeinfra[Build Agents/Compute]
    end
    subgraph infrapip[Infrastructure Pipeline]
      iacpipecode["IaC Pipeline Definition (code)"]
      iacpipedeps["IaC Pipeline Dependencies (modules)"]
    end
    subgraph bldpip[Application Build Pipeline]
      bldpipecode["Build Pipeline Definition (code)"]
      bldpipedeps["Build Pipeline Dependencies (modules)"]
    end
    subgraph dpypip[Application Deploy Pipeline]
      dpypipecode["Deploy Pipeline Definition (code)"]
      dpypipedeps["Deploy Pipeline Dependencies (modules)"]
    end
	end

classDef class1 fill: black, stroke: black
class s1 class1
classDef class2 fill: dgrey, stroke: dgrey
class Bootstrap,autplat,infrapip,bldpip,dpypip class2
:::