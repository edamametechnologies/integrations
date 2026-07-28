This repository contains the configuration guides and examples for enterprise integrations within the EDAMAME Hub.

## Provider setup guides

* Azure Access Control guide: [Setting Up Azure for Access Control Integration](https://github.com/edamametechnologies/integrations/wiki/Setting-Up-Azure-for-Access-Control-Integration)

* Google Access Control guide: [Setting Up Google for Access Control Integration](https://github.com/edamametechnologies/integrations/wiki/Setting-Up-Google-for-Access-Control-Integration)

* GitHub Access Control guide: [Setting Up GitHub for Access Control Integration](https://github.com/edamametechnologies/integrations/wiki/Setting-Up-GitHub-for-Access-Control-Integration)

* GitLab Access Control guide: [Setting Up GitLab for Access Control Integration](https://github.com/edamametechnologies/integrations/wiki/Setting-Up-GitLab-for-Access-Control-Integration)

* Netbird Access Control guide: [Setting Up Netbird for Access Control Integration](https://github.com/edamametechnologies/integrations/wiki/Setting-Up-Netbird-for-Access-Control-Integration)

* Tailscale Access Control guide: [Setting Up Tailscale for Access Control Integration](https://github.com/edamametechnologies/integrations/wiki/Setting-Up-Tailscale-for-Access-Control-Integration)

* Fortigate Access Control guide: [Setting Up Fortigate for Access Control Integration](https://github.com/edamametechnologies/integrations/wiki/Setting-Up-Fortigate-for-Access-Control-Integration)

* Netskope Access Control guide: [Setting Up Netskope for Access Control Integration](https://github.com/edamametechnologies/integrations/wiki/Setting-Up-Netskope-for-Access-Control-Integration) — documented ahead of release; the Netskope-side configuration can be prepared now, but the provider is not yet selectable in EDAMAME Hub.

## Integration principles and architecture

Every integration rests on the same separation: **EDAMAME decides which devices are compliant, your provider decides what compliance grants, and EDAMAME only keeps a container in sync between the two.** EDAMAME never authors your policy — it maintains the membership of an IP range list, a firewall address group, a device tag or an authorization flag that you created, and your own rule reads that membership.

The first decision when integrating a new product is **which device identifier to key on**, and it is dictated by the product rather than by preference: the identifier has to be one the enforcement point can actually observe. Three archetypes cover the products integrated so far.

* **Internet access control, keyed on public IP** — Entra ID, Google Cloud Identity, GitHub, GitLab. The enforcement point sits in the network path to a cloud resource, so what it observes is the public address the device connects from. The container is a list of IP ranges that policy references, rewritten in full on every change. Note that devices sharing one NAT egress are indistinguishable, so this grants network-location trust rather than device trust.

* **LAN access control, keyed on MAC address** — Fortigate. The enforcement point sits on the local segment and sees the device's MAC directly, which identifies the device rather than the network behind it. The container is a named group of address objects. It only works where the appliance is Layer 2 adjacent to the device, and it requires the appliance's management API to be reachable from EDAMAME's backend.

* **Overlay and endpoint agents, keyed on a vendor identity** — Netbird, Tailscale, Netskope. The enforcement point is the vendor's own client on the device, identifying devices by a peer ID, node ID or device UID it issued itself. EDAMAME must first learn that identity, so these integrations additionally require the vendor's client to be installed and connected and the EDAMAME agent to run with elevated privileges.

A product can be integrated when its API exposes an endpoint that offers: a **mutable container the provider's own policy engine can key on**; a **stable name or ID** for that container, created by the operator rather than by EDAMAME; acceptance of an **identifier EDAMAME can supply**; write access under a **long-lived, non-interactive credential** (service account token or OAuth client credentials, never an interactive login); **reachability from EDAMAME's cloud backend**, since calls originate there and not from the device; and **idempotent writes**, because membership is pushed repeatedly and whole-list mode rewrites the entire set on every change.

Full reference, including the event pipeline and the per-identifier trade-offs: [Integration principles and architecture](https://github.com/edamametechnologies/integrations/wiki/Integration-principles-and-architecture)

## Custom conditional access

* Custom Conditional Access Control guide (using IP or MAC addresses): [Custom Conditional Access Control guide (using IP or MAC addresses)](https://github.com/edamametechnologies/integrations/wiki/Comprehensive-JSON-Configuration-Guide-for-IP-Allow-List-Management)
