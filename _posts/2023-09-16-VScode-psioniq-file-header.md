---
layout: distill
title: Psioniq file header for VScode
description: Cài đặt extension Psioniq để tự động chèn file header
date: 2023-09-15
tags: core
categories: vn

authors:
  - name: Nguyen Canh Trung
    url: "https://bobibo.one"
    affiliations:
        name: bobibo.one

bibliography: 2018-12-22-distill.bib

# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: 1. Cài đặt psioniq extension
  - name: 2. Thiết lập
    subsections:
    - name: a. `psi-header.config`
    - name: b. `psi-header.changes-tracking`
    - name: c. `psi-header.lang-config`


# Below is an example of injecting additional post-specific styles.
# If you use this post as a template, delete this _styles block.
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }

---
## 1. Cài đặt psioniq extension



## 2. Thiết lập

Cài đặt `psioniq` bằng cách truy cập:  `File` > `Preferences` > `Settings` > `Extensions` > `psioniq File Header` > `Editing settings.json`

### a. `psi-header.config`

```
"psi-header.config": {
        "forceToTop": true,
        "blankLinesAfter": 6,
        "spacesBetweenYears": false,
        "license": "MIT",
        "author": "Nguyen Canh Trung",
        "initials": "NCT",
        "authorEmail": "nguyencanhtrung 'at' me 'dot' com",
        "company": "",
        "copyrightHolder": "",
        "creationDateZero": "asIs",
        "hostname": ""
    }
```

### b. `psi-header.changes-tracking`

```
  "psi-header.changes-tracking": {
        "isActive": true,
        "modAuthor": "Modified By",
        "modDate": "Last Modified",
        "modDateFormat": "YYYY-MM-DD HH:mm:ss",
        "include": [],
        "includeGlob": [],
        "exclude": [
            "markdown",
            "json",
            "jsonc",
            "shellscript"
        ],
        "excludeGlob": [
            "./**/*/ignoreme.*"
        ],
        "autoHeader": "autoSave",
        "enforceHeader": false,
        "updateLicenseVariables": false
    }
```

### c. `psi-header.lang-config`

```
"psi-header.lang-config": [
    {
        "language": "*",
        "begin": "// ----------------------------------------------------------------------------",
        "prefix": "// ",
        "suffix": "",
        "lineLength": 80,
        "end": "// ----------------------------------------------------------------------------",
        "forceToTop": true,
        "blankLinesAfter": 0
    },
    {
        "language": "systemverilog",
        "begin": "//-----------------------------------------------------------------------------",
        "prefix": "// ",
        "suffix": "",
        "lineLength": 80,
        "end": "//-----------------------------------------------------------------------------",
        "forceToTop": true,
        "blankLinesAfter": 0,
        "afterHeader": [
            "`timescale 1ns / 1ps"
        ]
    },
    {
        "language": "verilog",
        "mapTo": "systemverilog"
    }
]
```

### d. `psi-header.templates`

```
"psi-header.templates": [
    {
        "language": "*",
        "template": [
            "",
            "Project   : ",
            "Filename  : <<filenamebase>>",
            "",
            "Author    : <<author>>",
            "Email     : <<authoremail>>",
            "Date      : <<filecreated('YYYY-MM-DD HH:mm:ss')>>",
            "Last Modified : <<dateformat('YYYY-MM-DD HH:mm:ss')>>",
            "Modified By   : <<author>>",
            "",
            "Description: ",
            "",
            "HISTORY:",
            "Date      \tBy\tComments",
            "----------\t---\t---------------------------------------------------------"
        ],
        "changeLogCaption": "HISTORY:",
        "changeLogHeaderLineCount": 2,
        "changeLogEntryTemplate": [
            "<<dateformat(YY-MM-DD)>>\t<<initials>>\t"
        ]
    },
    {
        "language": "systemverilog",
        "template": [
            "",
            "Project   : ",
            "Module    : <<filenamebase>>",
            "Parent    : ",
            "Children  : ",
            "",
            "Author    : <<author>>",
            "Email     : <<authoremail>>",
            "Date      : <<filecreated('YYYY-MM-DD HH:mm:ss')>>",
            "Last Modified : <<dateformat('YYYY-MM-DD HH:mm:ss')>>",
            "Modified By   : <<author>>",
            "",
            "Description: ",
            "", 
            "Parameters:",
            "",
            "Multicycle and False Paths: ",
            "", 
            "HISTORY:",
            "Date      \tBy\tComments",
            "----------\t---\t---------------------------------------------------------"  
        ],
        "changeLogCaption": "HISTORY:",
        "changeLogHeaderLineCount": 2,
        "changeLogEntryTemplate": [
            "<<dateformat(YY-MM-DD)>>\t<<initials>>\t"
        ]
    }
]
```

## 3. Cấu hình hoàn thiện

```
{
    "workbench.colorTheme": "Default High Contrast",
    "code-runner.runInTerminal": true,
    "files.autoSave": "afterDelay",
    "[python]": {
        "editor.formatOnType": true
    },
    "editor.inlineSuggest.enabled": true,
    "git.autofetch": true,
    "explorer.confirmDelete": false,
    "editor.fontSize": 13,
    "editor.formatOnPaste": true,
    "editor.multiCursorModifier": "ctrlCmd",
    "editor.snippetSuggestions": "top",
    "security.workspace.trust.untrustedFiles": "open",
    "psi-header.config": {
        "forceToTop": true,
        "blankLinesAfter": 6,
        "spacesBetweenYears": false,
        "license": "MIT",
        "author": "Nguyen Canh Trung",
        "initials": "NCT",
        "authorEmail": "nguyencanhtrung 'at' me 'dot' com",
        "company": "",
        "copyrightHolder": "",
        "creationDateZero": "asIs",
        "hostname": ""
    },
    "psi-header.changes-tracking": {
        "isActive": true,
        "modAuthor": "Modified By",
        "modDate": "Last Modified",
        "modDateFormat": "YYYY-MM-DD HH:mm:ss",
        "include": [],
        "includeGlob": [],
        "exclude": [
            "markdown",
            "json",
            "jsonc",
            "shellscript"
        ],
        "excludeGlob": [
            "./**/*/ignoreme.*"
        ],
        "autoHeader": "autoSave",
        "enforceHeader": false,
        "updateLicenseVariables": false
    },
    "psi-header.lang-config": [
        {
            "language": "*",
            "begin": "// ----------------------------------------------------------------------------",
            "prefix": "// ",
            "suffix": "",
            "lineLength": 80,
            "end": "// ----------------------------------------------------------------------------",
            "forceToTop": true,
            "blankLinesAfter": 0
        },
        {
            "language": "vhdl",
            "begin": "-- ----------------------------------------------------------------------------",
            "prefix": "-- ",
            "suffix": "",
            "lineLength": 80,
            "end": "-- ----------------------------------------------------------------------------",
            "forceToTop": true,
            "blankLinesAfter": 0,
            "afterHeader": [
                "-- Language: VHDL-1993",
                "",
                "library ieee;",
                "\tuse ieee.std_logic_1164.all;"
            ]
        },
        {
            "language": "systemverilog",
            "begin": "//-----------------------------------------------------------------------------",
            "prefix": "// ",
            "suffix": "",
            "lineLength": 80,
            "end": "//-----------------------------------------------------------------------------",
            "forceToTop": true,
            "blankLinesAfter": 0,
            "afterHeader": [
                "`timescale 1ns / 1ps"
            ]
        },
        {
            "language": "verilog",
            "mapTo": "systemverilog"
        }
    ],
    "psi-header.templates": [
        {
            "language": "*",
            "template": [
                "",
                "Project   : ",
                "Filename  : <<filenamebase>>",
                "",
                "Author    : <<author>>",
                "Email     : <<authoremail>>",
                "Date      : <<filecreated('YYYY-MM-DD HH:mm:ss')>>",
                "Last Modified : <<dateformat('YYYY-MM-DD HH:mm:ss')>>",
                "Modified By   : <<author>>",
                "",
                "Description: ",
                "",
                "HISTORY:",
                "Date      \tBy\tComments",
                "----------\t---\t---------------------------------------------------------"
            ],
            "changeLogCaption": "HISTORY:",
            "changeLogHeaderLineCount": 2,
            "changeLogEntryTemplate": [
                "<<dateformat(YY-MM-DD)>>\t<<initials>>\t"
            ]
        },
        {
            "language": "systemverilog",
            "template": [
                "",
                "Project   : ",
                "Module    : <<filenamebase>>",
                "Parent    : ",
                "Children  : ",
                "",
                "Author    : <<author>>",
                "Email     : <<authoremail>>",
                "Date      : <<filecreated('YYYY-MM-DD HH:mm:ss')>>",
                "Last Modified : <<dateformat('YYYY-MM-DD HH:mm:ss')>>",
                "Modified By   : <<author>>",
                "",
                "Description: ",
                "", 
                "Parameters:",
                "",
                "Multicycle and False Paths: ",
                "", 
                "HISTORY:",
                "Date      \tBy\tComments",
                "----------\t---\t---------------------------------------------------------"  
            ],
            "changeLogCaption": "HISTORY:",
            "changeLogHeaderLineCount": 2,
            "changeLogEntryTemplate": [
                "<<dateformat(YY-MM-DD)>>\t<<initials>>\t"
            ]
        },
        {
            "language": "vhdl",
            "template": [
                "",
                "Project   : ",
                "Module    : <<filenamebase>>",
                "Parent    : ",
                "Children  : ",
                "",
                "Author    : <<author>>",
                "Email     : <<authoremail>>",
                "Date      : <<filecreated('YYYY-MM-DD HH:mm:ss')>>",
                "Last Modified : <<dateformat('YYYY-MM-DD HH:mm:ss')>>",
                "Modified By   : <<author>>",
                "",
                "Description: ",
                "",
                "Parameters:",
                "",
                "Multicycle and False Paths: ",
                "",
                "HISTORY:",
                "Date      \tBy\tComments",
                "----------\t---\t---------------------------------------------------------"
            ],
            "changeLogCaption": "HISTORY:",
            "changeLogHeaderLineCount": 2,
            "changeLogEntryTemplate": [
                "<<dateformat(YY-MM-DD)>>\t<<initials>>\t"
            ]
        }
    ],
}
```