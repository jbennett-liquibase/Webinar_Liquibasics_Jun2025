<p align="left">
  <img src="img/liquibase.png" alt="Liquibase Logo" title="Liquibase Logo" width="324" height="72">
</p>

# 👋 Welcome to Liquibase!
This repository contains the demo scenario for the "Liquibasics" webinar originally delivered on June 26th, 2025. Recordings of all webinars can be found [here](https://www.liquibase.com/videos).

# 🔧 Demo Requirements
Liquibase Pro is a java based application with [minimal requirements](https://docs.liquibase.com/start/install/liquibase-requirements.html).

A Pro key is required to utilize Policy Checks. A temporary key is provided for webinar attendees in the [liquibase.properties](liquibase.properties) file.

If you need a new Pro key, you can request one [here](https://www.liquibase.com/trial).

# ✔️ Pre-Demo Steps
1. Clone this repository
    * [Instructions](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository)
1. Download and install Liquibase CLI
    * [Installation Link](https://www.liquibase.com/download)
1. Open a command prompt (Windows) or terminal (Linux/Mac) and start an [H2 Database](https://contribute.liquibase.com/extensions-integrations/directory/database-tutorials/h2/)<br>
  H2 is a temporary in-memory database suitable for testing
    ```
    liquibase init start-h2
    ```
    *Safe to close browser window but leave command prompt/terminal open*
# 📋 Demo Steps
1. ⌨️ Command/Terminal Prompt<br>
    Open a new command prompt/terminal window and change to the folder were the repository was cloned.
1. 🧩 Regular Expression (regex) Policy Check<br>
    Liquibase has a command-line Wizard to assist in creating policy checks.
    Let's create a new one to look for delete statements which do not have a where clause.
    ```
    liquibase checks customize --check-name=SqlUserDefinedPatternCheck
    ```
    Liquibase will prompt us for all the information we need to create this check. Here are the answers to those prompts.

    | Prompt | Response |
    |--------|----------|
    | Short Name | NoDeleteWithoutWhere |
    | Severity | 1 |
    | Search String | ``` (?is)(?=.*\b(delete)\b)(?!.*\b(where)\b).* ```|
    | Message | All DELETE statements must have a WHERE clause. |
    | Strip Comments| true |
    | PATH_FILTER_REGEX | |
    | Split Statements | true |

    The output should display this at the end:
    ```
    Liquibase command 'checks customize' was executed successfully.
    ```
1. 🐍 Python Check<br>
    The built-in and regex checks are very flexible but sometimes you need to create your own policy checks. With Liquibase Pro, you can build your own using Python. Let's create a new one using the same wizard.
    ```
    liquibase checks customize --check-name=CustomCheckTemplate
    ```
    The wizard will have a few more prompts for our python check. Here are the answers we will use.
    | Prompt | Response |
    |--------|----------|
    | Short Name | PKNamingConvention |
    | Severity | 0 |
    | Description | Primary key names must include table name. |
    | Scope | database |
    | SCRIPT_MESSAGE | Primary key name \_\_CURRENT_NAME\_\_ must include table name (\_\_NAME_STANDARD\_\_). |
    | SCRIPT_PATH | Scripts/pk_names.py |
    | SCRIPT_ARGS | |

    The output should display this at the end:
    ```
    Liquibase command 'checks customize' was executed successfully.
    ```
    Congratulations!!! You have created two new policy checks! 🎉
1. 🚀 Ready for deployment!<br>
    We've configured our checks, so lets try a deployment.
    
    Liquibase [workflows](https://docs.liquibase.com/commands/flow/flow.html) allow you to define a standard set of deployment steps for your database changes. In this way you can always ensure your deployments are done consistently following your best practices, regardless of automation tool or database platform.
    ```
    liquibase flow
    ```
    The flow will run several Liquibase commands. At the end, you should see this:
    ```
    ERROR: Exception Details
    ERROR: Exception Primary Class:  CommandFailedException
    ERROR: Exception Primary Reason:  Checks run command exited with error code of 1
    ERROR: Exception Primary Source:  4.32.0
    Checks run command exited with error code of 1
    ```
1. ✅ Review and fix the error<br>
    Our new policy check flagged a changeset as an error. ⚠️ **Note:** the error code matches the severity we set!
    
    Lets open the "Checks-run-report.html" [operation report]((https://docs.liquibase.com/liquibase-pro/observability/operation-reports.html)) to troubleshoot the issue.

    Now that we know what happened, we can edit the [changelog.main.xml](changelog.main.xml) file and remove lines 80-83. Once this is done, save the file and re-run our flow file.
    ```
    liquibase flow
    ```
    As before, the flow will run several Liquibase commands. At the end, you should see this:
    ```
    Liquibase command 'flow' was executed successfully.
    ```
    The history command, run at the end of the flow, displays all the database changes Liquibase has deployed. 🌟 **Bonus:** open the "Update-run-report.html" file to see the results of our deployment.
1. 🔄 Re-run our flow<br>
    Liquibase tracks all database changes it deploys, so we won't redeploy. 
    ```
    liquibase flow
    ```
    Open the "Checks-run-report.html" report. Our python check determined that one of our primary keys did not follow our naming convention! But because its set to information only, our deployments can proceed.
1. 🌟 Congratulations!!!<br>
    This concludes the Liquibase Pro test drive. You can continue to experiment or simply close the command prompt/terminal windows.

# 📒 Liquibase Documentation
* [Documentation Home](https://docs.liquibase.com/home.html)
* [Liquibase University](https://learn.liquibase.com/)

# 💡 Core Concepts
If you are unfamilar with Liquibase concepts, here is some information to get you started.

* [Changeset](https://docs.liquibase.com/concepts/changelogs/changeset.html): basic unit of database work
* [Changelog](https://docs.liquibase.com/concepts/changelogs/home.html): text file containing collection of changesets
* [Tracking tables](https://docs.liquibase.com/concepts/tracking-tables/tracking-tables.html): tables created and maintained by Liquibase

# ❓ Helpful Liquibase Commands
|Command |Description|Documentation
|----------|------------|------------|
| connect | Test database connection | [Link](https://docs.liquibase.com/commands/change-tracking/connect.html)
| flow | Execute a Liquibase workflow | [Link](https://docs.liquibase.com/commands/flow/flow.html)
| status | Show undeployed changes | [Link](https://docs.liquibase.com/commands/change-tracking/status.html)
| update | Run changes against target database | [Link](https://docs.liquibase.com/change-types/update.html)
| history | Show deployed changes | [Link](https://docs.liquibase.com/commands/change-tracking/history.html)
| rollback-one-update | Rollback the last or a specified update | [Link](https://docs.liquibase.com/commands/rollback/rollback-one-update.html)
| checks | Show or view policy checks | [Link](https://docs.liquibase.com/liquibase-pro/policy-checks/workflows/home.html)

# 🔗 Helpful Policy Check Links
* [Regex101](https://regex101.com/)
* [Python Code Reference](https://www.w3schools.com/python/default.asp)
* [Example Regex and Python Checks](https://github.com/liquibase/custom_policychecks)

# 🔦 Troubleshooting
* [Installation issues](https://docs.liquibase.com/start/install/liquibase-installation-troubleshooting.html)
* [Common issues](https://support.liquibase.com/hc/en-us/sections/27504481958555-Troubleshooting)
* [Liquibase University](https://learn.liquibase.com/catalog/info/id:127)

# ☎️ Contact Liquibase
* Liquibase sales: https://www.liquibase.com/contact

# ⭐ Thank you!
Thank you for evaluating Liquibase Pro! We hope to be a part of your DevOps journey.