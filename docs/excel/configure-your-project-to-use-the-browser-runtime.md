---
ms.date: 02/06/2020
title: "Configure your Excel add-in to use a shared runtime (preview)"
ms.prod: excel
description: Configure your Excel add-in to share the browser runtime and run ribbon, task pane, and custom function code in the same runtime.
localization_priority: Priority
---

# Configure your Excel add-in to share the browser runtime

You can confugre your Excel add-in to use a shared runtime. This will force all code for the ribbon, task pane, and custom functions, to run in the same browser JavaScript runtime environment. This means code for the ribbon, task pane, or custom functions can call each other and share global data. To configure your yo office project to use a shared runtime, follow the instructions in this article.

## Create the add-in project

If you are starting a new project, follow these steps to use the Yeoman generator to create an Excel add-in project. Run the following command and then answer the prompts with the following answers:

```command&nbsp;line
yo office
```

- Choose a project type: **Excel Custom Functions Add-in project**
- Choose a script type: **JavaScript**
- What do you want to name your add-in? **My Office Add-in**

![Screenshot of answering prompts from yo office to create the add-in project.](../images/yo-office-excel-project.png)

After you complete the wizard, the generator creates the project and installs supporting Node components.

## Configure the manifest

Follow these steps for a new or existing project to configure it to use a shared context browser runtime.

1. Start Visual Studio Code and open the **My Office Add-in** project.
2. Open the **manifest.xml** file.
3. Change the `<Requirements>` section to use **CustomFunctionsRuntime** version **1.2** as shown in the following code.
    
    ```xml
    <Requirements>
    <Sets DefaultMinVersion="1.1">
    <Set Name="CustomFunctionsRuntime" MinVersion="1.2"/>
    </Sets>
    </Requirements>
    ```
    
4. Find the `<VersionOverrides>` section, and add the following `<Runtimes>` section. The lifetime needs to be **long** so that the custom functions can still work even when the task pane is closed.
    
    ```xml
    <VersionOverrides xmlns="http://schemas.microsoft.com/office/taskpaneappversionoverrides" xsi:type="VersionOverridesV1_0">
      <Hosts>
        <Host xsi:type="Workbook">
        <Runtimes>
          <Runtime resid="TaskPaneAndCustomFunction.Url" lifetime="long" />
        </Runtimes>
        <AllFormFactors>
    ```
    
5. In the `<Page>` element, change the source location from **Functions.Page.Url** to **TaskPaneAndCustomFunction.Url**.

    ```xml
    <AllFormFactors>
    ...
    <Page>
    <SourceLocation resid="TaskPaneAndCustomFunction.Url"/>
    </Page>
    ...
    ```

6. In the `<DesktopFormFactor>` section, change the **FunctionFile** from **Commands.Url** to use **TaskPaneAndCustomFunction.Url**.
    
    ```xml
    <DesktopFormFactor>
    <GetStarted>
    ...
    </GetStarted>
    <FunctionFile resid="TaskPaneAndCustomFunction.Url"/>
    ```
    
7. In the `<Action>` section, change the source location from **Taskpane.Url** to **TaskPaneAndCustomFunction.Url**.
    
    ```xml
    <Action xsi:type="ShowTaskpane">
    <TaskpaneId>ButtonId1</TaskpaneId>
    <SourceLocation resid="TaskPaneAndCustomFunction.Url"/>
    </Action>
    ```
    
8. Add a new **Url id** for **TaskPaneAndCustomFunction.Url** that points to **taskpane.html**.
     
    ```xml
    <bt:Urls>
    <bt:Url id="Functions.Script.Url" DefaultValue="https://localhost:3000/dist/functions.js"/>
    ...
    <bt:Url id="TaskPaneAndCustomFunction.Url" DefaultValue="https://localhost:3000/taskpane.html"/>
    ...
    ```
    
9. Save your changes and rebuild the project.
    
    ```command&nbsp;line
    npm run build
    ```

## Next Steps

Now try out some features of the shared runtime by taking a look at the following articles.

- [Call Excel APIs from a custom function](call-excel-apis-from-custom-function.md)
- [Run code in your Excel add-in when the document opens](run-code-on-document-open.md)