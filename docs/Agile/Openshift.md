<?add topic='Helm'?>

## Setup

    # Follow instructions from GUI setup and download client
    oc login

Settings are stored in ~/.kube/config

## CLI Commands

Note: all Kubernetes commands work with the oc client. So also check <?add topic='Kubernetes'?>

    oc login [<cluster>]
    oc projects              # List projects
    oc project <project>     # Switch project

## Working with Templates

Create a template from an existing project

    oc export all --as-template my_project_template >template.json
    
Note that the resulting JSON will have no parameters yet. You'll have to 
identify places to replace with parameters (syntax is "${PARAMETER}") and
to declare those parameters.

## Orchestration

- [Using Terraform with Openshift](https://medium.com/@fabiojose/platform-as-code-with-openshift-terraform-1da6af7348ce)

## Misc

-   ebook: [OpenShift for
    Developers](https://www.openshift.com/promotions/for-developers.html)
-   OpenShift Cheat Sheets
    -   [http://akrambenaissi.com/2015/11/12/openshift-cheat-sheet-for-beginners/](http://akrambenaissi.com/2015/11/12/openshift-cheat-sheet-for-beginners/)
    -   https://monodot.co.uk/openshift-cheatsheet/
