# Ensono SRE - Configuration Driven Maintenance
CDM is a framework that allows for automated maintenance checks across various cloud proviers & cloud resources

This repository acts as the central hub for the CDM Framework. 
It provides links to all related repositories and will contain all documentation for the framework once produced. 

## Repositories

### cdm-cli
https://github.com/IamSamD/cdm-cli

The cdm-cli is the orchestrator that allows for the running of checks in a pipeline based on simple configuration.

It also acts as a utility tool for developers allowing the easy scaffolding of code for new checks.


### cdm-checks
https://github.com/IamSamD/cdm-checks

cdm-checks is the central library in which all checks are held. This allows checks to be used in a modular way across many CDM setups for different clients.


### cdm_framework
https://github.com/IamSamD/cdm_framework

The CDM_Framework is a go package (or library) that exposes various types and utility functions used in cdm checks and the cdm-cli. 

This allows a higher level developer experience for developers writing new checks and also ensures that certain shared behaviours are standardised accross checks. 


### cdm_pipeline

This is the pipleine template to be used when setting up cdm for a new client. It contains the standard pipeline that can be cloned and then edited to set up the automation for a client. 


### cdm_check_template
https://github.com/IamSamD/cdm_check_template

The cdm_check_template repo holds the tempalte for a new CDM check and is used by the cdm-cli to scaffold a new check in the cdm-checks repo.

## Developing with GitHub Copilot

To assist with development, a comprehensive instructions file has been created: `copilot-instructions.md`.

When working with GitHub Copilot in this workspace, you can reference this file to give the AI agent full context of the system architecture, workflows, and coding standards.

**Setup for Automatic Context:**
To make these instructions automatically available to Copilot without manual referencing:
1.  Create a `.github` directory in the root of your repository (if it doesn't exist).
2.  Move or copy `copilot-instructions.md` into that directory.
    *   Path: `.github/copilot-instructions.md`
3.  Copilot will now automatically read this file for context in every interaction.

**Manual Usage:**
1.  Open `copilot-instructions.md` in your editor or add it to the Copilot context.
2.  Ask Copilot to "Read the CDM instructions to understand how to add a new check" or "Explain the data flow based on the instructions".
