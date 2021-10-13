# Dynamicweb Intellisense sample VS Code project for MAC and PC
Sample project setup using .NET Standard to provide intellisense in templates on MAC and PC using VS Code

To get started clone this repository
```
git clone https://github.com/dynamicweb/NetStandardIntellisenseProject.git
```

When the repository has been cloned, remove the ```/Files``` folder in the project and copy in you ```/Files``` templates from your project.

When you publish the templates, only publish ```/Files``` and not the root of the project

## Create a new VS Code project
As an alternative create a new VS Code project, create a web sdk project and install Dynamicweb nuget packages.
```
dotnet new web
dotnet add package Dynamicweb
dotnet add package Dynamicweb.Rendering.Providers.NetCore
dotnet add package Dynamicweb.Ecommerce
```


## Get intellisense in templates
To get the right intellisense in templates, the Razor template has to have the correct Inherits statement.

There are basically 2 types of templates
- Viewmodel based templates using ```ViewModelBase``` based viewmodels.
- Template tag templates using ```GetValue("")``` tag notation (Classic templates).

### ViewModel based template intellisense
To enable intellisense in a viewmodel based template, add an @inherits Dynamicweb.Rendering.ViewModelTemplate<> statement in the first line of the template. It is a generic, and the generic type is the ViewModel that should be used in the template.

Examples

**Layout and master templates**

```
@inherits Dynamicweb.Rendering.ViewModelTemplate<Dynamicweb.Frontend.PageViewModel>
@using System
@using Dynamicweb
@using Dynamicweb.Frontend

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@Model.Title</title>

  </head>
  <body>
    <h1>Welcome to @Model.Name</h1>
  </body>
</html>
```

**Paragraph template**
```
@inherits Dynamicweb.Rendering.ViewModelTemplate<Dynamicweb.Frontend.ParagraphViewModel>

<h1>@Model.Header</h1>
```

**Navigation template**
```
@inherits Dynamicweb.Rendering.ViewModelTemplate<Dynamicweb.Frontend.Navigation.NavigationTreeViewModel>

<ul>
    @foreach (var node in Model.Nodes)
    {
        <li>
            <a href="@node.Link">@node.Name</a>

            if(node.Nodes.Any()){
                //There are subnodes
                <ul>
                    @foreach (var subnode in node.Nodes){
                        <li><a href="@subnode.Link">@subnode.Name</a></li>
                    }
                </ul>
            }

        </li>
    }
</ul>
```

**Product list template**
```
@inherits Dynamicweb.Rendering.ViewModelTemplate<Dynamicweb.Ecommerce.ProductCatalog.ProductListViewModel>

<h1>@Model.Group.Name</h1>

<ul>
@foreach (var product in Model.Products)
{
    <li>@product.Name (@product.Price.PriceWithVatFormatted)</li>
}
</ul>
```

**Product template**
```
@inherits Dynamicweb.Rendering.ViewModelTemplate<Dynamicweb.Ecommerce.ProductCatalog.ProductViewModel>

<h1>@Model.Name</h1>
<p>
    @Model.Price.PriceWithVatFormatted
</p>
```

### Templeate tag based templates
All tag based templates use the same inherits statement - so it is only the use of the template that decides which tags are available for GetValue() in a given template.


```
@inherits Dynamicweb.Rendering.RazorTemplateBase<Dynamicweb.Rendering.RazorTemplateModel<Dynamicweb.Rendering.Template>>

<h1>@GetString("Ecom:ProductList:Page.GroupName")</h1>

<ul>
@foreach (LoopItem product in GetLoop("Products"))
{
    <li>@product.GetString("Ecom:Product.Name") (@product.GetString("Ecom:Product.Price.PriceWithVATFormatted"))</li>
}
</ul>
```

Use ```@TemplateTags()``` to see a complete list of all tags available in a template

Use ```@TemplateTags("search")``` to search for tags with a specific string in it.

### ViewModel based template possibilities
These apps and features have ViewModels
- Masters and Layouts (PageViewModel)
- Paragraphs (ParagraphViewModel)
- Navigations (NavigationTreeViewModel)
- Product catalog for viewmodels
  - ProductList (ProductListViewModel)
  - Product Detail (ProductViewModel)
- Customer Center
  
  A list of all viewmodels can be found in the API documentation https://doc.dynamicweb.com/api/html/c0c19636-f848-e866-6208-ade44285310b.htm

## Limitations
Using helpers in templates under this setup will not give you intellisense since helpers do not exist on ASP.NET Core.

So code like below will give you red and yellow warnings when authoring templates, but will work when running them on a real Dynamicweb 9 installation.

```
<div>
	@RenderBody()
</div>

@helper RenderBody()
{
}
```
