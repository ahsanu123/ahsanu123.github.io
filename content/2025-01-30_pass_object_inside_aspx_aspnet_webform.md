---
date: 2025-01-30
title: 🍼 Passing Object From .ASCX in ASPNET Webform.
tags: linux, howto, ASPNET, legacy
---

> **copied from DeepSeek Answer**

# Steps to Pass an Object from .ascx File

### 1. Define a Public Property in the User Control

In the code-behind file of your User Control (e.g., MyUserControl.ascx.cs), define a public property that will hold the object.

```c#
public partial class MyUserControl : System.Web.UI.UserControl
{
    // Public property to hold the object
    public MyCustomObject MyObject { get; set; }

    protected void Page_Load(object sender, EventArgs e)
    {
        if (MyObject != null)
        {
            // Use the object in your logic
            Label1.Text = MyObject.SomeProperty;
        }
    }
}
```

### 2. Define the Object in the Parent Page

In the parent page (e.g., Default.aspx), you can declare the object and pass it to the User Control declaratively.

Example of MyCustomObject Class:

```c#
public class MyCustomObject
{
    public string SomeProperty { get; set; }
}
```

### 3. Pass the Object Declaratively in .ascx

In the .aspx file where you use the User Control, you can set the property declaratively. However, since you cannot directly declare an object in the markup, you can use a static method or a factory method to create the object and assign it to the property.

Example:
```html
<%@ Page Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="WebApplication1.Default" %>
<%@ Register Src="~/MyUserControl.ascx" TagName="MyUserControl" TagPrefix="uc1" %>
<%@ Import Namespace="WebApplication1" %>

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
    <title>Pass Object to User Control</title>
</head>
<body>
    <form id="form1" runat="server">
        <div>
            <uc1:MyUserControl ID="MyUserControl1" runat="server" 
                MyObject='<%# new MyCustomObject { SomeProperty = "Hello from .ascx!" } %>' />
        </div>
    </form>
</body>
</html>
```

### 4. Bind the Property Using Data Binding

In the example above, the MyObject property is set using <%# %> data binding syntax. To ensure the binding is evaluated, you need to call the DataBind() method in the Page_Load event of the parent page.

Parent Page Code-Behind (Default.aspx.cs):

```c#
protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
        // Ensure data binding is evaluated
        DataBind();
    }
}
```

Explanation of the Code

Public Property: The MyObject property is defined in the User Control's code-behind.

Declarative Assignment: The MyObject property is set in the .aspx file using <%# %> data binding syntax.

Data Binding: The DataBind() method is called in the parent page's Page_Load event to ensure the data binding expression is evaluated.

Alternative: Use a Static Method
If you want to avoid data binding, you can use a static method to create the object and assign it to the property.

Static Method Example:
```c#
public static class ObjectFactory
{
    public static MyCustomObject CreateObject()
    {
        return new MyCustomObject { SomeProperty = "Hello from Static Method!" };
    }
}
```

In .aspx File:

```html
<uc1:MyUserControl ID="MyUserControl1" runat="server" 
    MyObject='<%= ObjectFactory.CreateObject() %>' />
```
### Key Points

- You cannot directly declare an object in the .aspx or .ascx markup. Instead, you use data binding (<%# %>) or static methods (<%= %>) to create and assign the object.

- Ensure you call DataBind() in the parent page if you use data binding (<%# %>).

- This approach works for User Controls and can be adapted for Custom Server Controls as well.

If you have further questions or need clarification, feel free to ask! 😊

