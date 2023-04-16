# iac-homelab

## Objectifs

L’objectif principal de ce projet est de vous aider à monter en compétences sur
les outils que l’on utilise le plus souvent dans la mise en place d’une démarche
Devops :

- [**Docker**](https://blog.stephane-robert.info/tags/docker/) pour construire les images qui seront utilisé dans les
  pipelines CI/CD
- [**Vagrant**](https://blog.stephane-robert.info/tags/vagrant/) pour instancier sur votre PC de travail des stacks
  complètes sur des machines virtualisées en utilisant libvirt.
- [**Terraform**](https://blog.stephane-robert.info/tags/terraform/) pour créer les
  machines virtuelles sur les mini-pc et cela par la suite chez des clouders
- [**GitLab**](https://blog.stephane-robert.info/tags/gitlab/) et GitHub pour faire tourner les pipelines CI/CD
- [**Ansible**](https://blog.stephane-robert.info/tags/ansible/) pour configurer les machines virtuelles
- [**Kubernetes**](https://blog.stephane-robert.info/tags/kubernetes/) pour apprendre à installer et à gérer des
  applications Cloud Natives.
- **Prometheus** pour le monitoring.
- D'autres outils au besoin.

## Composition de Mon Home Lab

Mon **Home Lab Devops** se compose actuellement de :

- **deux mini-pc** :
  - Un [UM450 de MinisForum](https://www.amazon.fr/dp/B0BLC1LXF5?psc=1&ref=ppx_yo2ov_dt_b_product_details)
  - Un [UM450 de MinisForum](https://www.amazon.fr/dp/B0BLC1LXF5?psc=1&ref=ppx_yo2ov_dt_b_product_details)
- un switch Ethernet 24 ports POE

Sur le premier mini-pc tourne :

- un **k3s mono noeud** hébergeant les [runnrs
  gitlab](https://blog.stephane-robert.info/post/gitlab-kubernetes-runner/) et [Ansible
  AWX](https://blog.stephane-robert.info/post/ansible-awx-operator-installation-kubernetes/)
- un serveur NFS pour y déposer les données persistantes

Sur le second :

- **Libvirt** est activé et prêt à héberger des applications à la demande du
  type : [**Nexus Repository
  Manager**](https://blog.stephane-robert.info/post/devops-artefacts-nexus-docker-registry/),
  **Rundeck** (en cours d'écriture). D'autres vont suivre. Le tout sera
  instancié avec du **Terraform**.

## Provisionnement des machines

J'ai fait l'installation de la **version server d'Ubuntu 21.10** avec juste le
package OpenSSH (synchro des clés depuis mon compte github). J'ai autorisé le
compte *admuser*, créé pendant l'installation, à utiliser [sudo sans mot de
passe](https://www.makeuseof.com/using-sudo-without-password/).

**Pour simplifier la gestion des machines :**

- Sur ma box internet, j'ai alloué des baux statiques.
- J'ai ajouté au fichier /etc/hosts de la machine depuis laquelle je
  fais l'administration les adresses IP.

## Première Installation

Clonez ce repository sur votre machine servant à l'installation.

```bash
git clone https://gitlab.com/b4288/infra-as-code-homelab.git
```

Avant de passer à l'installation il faut [générer des certificats avec
mkcert](https://blog.stephane-robert.info/post/homelab-certificats-https-ssl-mkcert/)



```bash
asdf plugin add mkcert https://github.com/salasrod/asdf-mkcert.git
asdf install mkcert latest
asdf global mkcert latest
```

## Génération du CA

mkcert -install

cd certificats
mkcert 'devbox1.robert.local' localhost 127.0.0.1 ::1
mkcert 'devbox2.robert.local' localhost 127.0.0.1 ::1
```

Une fois les machines provisionnées les installations sont faites avec
**Ansible** :

```bash
## Installation des outils de base
ansible-playbook -i inventory playbooks/install.yml
## Installation de Kubernetes et Libvirt
ansible-playbook -i inventory playbooks/provision.yml
```

Avant, il faudra certainement adapter certaines choses dans les **playbooks** ou
dans **l'inventaire**.

Vous pouvez aussi utiliser le Vagrantfile pour tester le tout avant :

```bash
vagrant up
# On utilise l'inventaire généré par vagrant
ansible-playbook -i ./.vagrant/provisioners/ansible/inventory playbooks/install.yml
```

## La suite

**L'automatisation de tout le reste AWX, Gitlab runners, Nexus, Rundeck, ...**
