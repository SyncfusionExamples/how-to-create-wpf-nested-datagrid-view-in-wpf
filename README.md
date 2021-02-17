# How to Create WPF Nested DataGrid View (Master Details View) in WPF?

## About the sample

This example illustrates how to create wpf nested datagrid view (master details view)?

[WPF DataGrid](https://www.syncfusion.com/wpf-ui-controls/datagrid) provides support to represent the hierarchical data in the form of nested tables using Master-Details View. You can expand or collapse the nested tables by using an expander in a row or programmatically.

## Generating Master-Details view from IEnumerable
Master-Details View’s relation can be generated for the properties of type [IEnumerable](https://msdn.microsoft.com/en-us/library/system.collections.ienumerable.aspx) exists in the underlying data object. 

### Auto-generating relations

SfDataGrid can automatically generate relations and inner relations for the IEnumerable type properties in the underlying data object. This can be enabled by setting [SfDataGrid.AutoGenerateRelations](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html#Syncfusion_UI_Xaml_Grid_SfDataGrid_AutoGenerateRelations) to true.

``` Xaml

<syncfusion:SfDataGrid x:Name="dataGrid" 
                               AutoGenerateColumns="True"
                               AutoGenerateRelations="True"
                               ItemsSource="{Binding OrdersDetails}" />

``` 

When relations are auto-generated, you can handle the [SfDataGrid.AutoGeneratingRelations](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html) event to customize or cancel the GridViewDefinition before they are added to the [SfDataGrid.DetailsViewDefinition](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html#Syncfusion_UI_Xaml_Grid_SfDataGrid_DetailsViewDefinition).

### Manually defining Relations

You can define the Master-Details View’s relation manually using [SfDataGrid.DetailsViewDefinition](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html#Syncfusion_UI_Xaml_Grid_SfDataGrid_DetailsViewDefinition), when the [SfDataGrid.AutoGenerateRelations](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html#Syncfusion_UI_Xaml_Grid_SfDataGrid_AutoGenerateRelations) is false.

To define Master-Details View relations, create GridViewDefinition and set the name of IEnumerable type property (from data object) to ViewDefinition.RelationalColumn. Then, add the GridViewDefinition to the SfDataGrid.DetailsViewDefinition.

``` Xaml

<syncfusion:SfDataGrid x:Name="dataGrid"
                               AutoGenerateColumns="True"
                               AutoGenerateRelations="False"
                               ItemsSource="{Binding Path=OrdersDetails}">
    <syncfusion:SfDataGrid.DetailsViewDefinition>
        <syncfusion:GridViewDefinition RelationalColumn="OrderDetails">
            <syncfusion:GridViewDefinition.DataGrid>
                <syncfusion:SfDataGrid x:Name="FirstDetailsViewGrid">
                </syncfusion:SfDataGrid>
            </syncfusion:GridViewDefinition.DataGrid>
        </syncfusion:GridViewDefinition>
    </syncfusion:SfDataGrid.DetailsViewDefinition>
</syncfusion:SfDataGrid>

```

![DetailsView_IEnumerable](Images/DetailsView_IEnumerable.png)

## Generating Master-Details view from DataTable
Master-Details View’s relation can be generated for DataTable, when [DataRelation](https://msdn.microsoft.com/en-in/library/system.data.datarelation.aspx) is defined between two tables in underlying DataSet.

### Create the DataTable with relations
You can create relation between tables using a DataSet as given below. 

``` c#

public class ViewModel : NotificationObject
{
    string connectionString;

    /// <summary>
    /// Initializes a new instance of the <see cref="ViewModel"/> class.
    /// </summary>
    public ViewModel()
    {
        DataCollection = GetDataTable();
    }

    private DataTable _dataCollection;

    /// <summary>
    /// Gets or sets the data collection.
    /// </summary>
    /// <value>The data collection.</value>
    public DataTable DataCollection
    {
        get { return _dataCollection; }
        set { _dataCollection = value; }
    }

    /// <summary>
    /// Gets the data table.
    /// </summary>
    /// <returns></returns>
    public DataTable GetDataTable()
    {
        DataSet ds = new DataSet();
        connectionString = string.Format(@"Data Source = {0}", @"..\..\Northwind.sdf");
        using (SqlCeConnection con = new SqlCeConnection(connectionString))
        {

            con.Open();
            SqlCeDataAdapter sda = new SqlCeDataAdapter("SELECT * FROM Suppliers", con);
            sda.Fill(ds, "Suppliers");
        }

        using (SqlCeConnection con1 = new SqlCeConnection(connectionString))
        {
            con1.Open();
            SqlCeDataAdapter sda1 = new SqlCeDataAdapter("SELECT * FROM Products", con1);
            sda1.Fill(ds, "Products");
        }
        ds.Relations.Add(new DataRelation("Supplier_Product", ds.Tables[0].Columns["Supplier ID"], ds.Tables[1].Columns["Supplier ID"]));

        if (ds.Tables.Count > 0)
            return ds.Tables[0];
        else
            return null;
    }
}

```

### Auto-generating relations

SfDataGrid will automatically generate relations and inner relations based on relations defined in DataSet. This can be enabled by setting [SfDataGrid.AutoGenerateRelations](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html#Syncfusion_UI_Xaml_Grid_SfDataGrid_AutoGenerateRelations) to true.

``` Xaml

<syncfusion:SfDataGrid Name="datagrid"
                               AutoGenerateRelations="True"
                               ItemsSource="{Binding DataCollection}" />


```


### Manually defining Relations
You can define the Master-Details View’s relation manually using SfDataGrid.DetailsViewDefinition, when the [SfDataGrid.AutoGenerateRelations](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html#Syncfusion_UI_Xaml_Grid_SfDataGrid_AutoGenerateRelations) is false.

To define Master-Details View relations, create GridViewDefinition and set the relation name Supplier_Product to ViewDefinition.RelationalColumn. Then, the GridViewDefinition is added to the SfDataGrid.DetailsViewDefinition collection of parent DataGrid.

``` Xaml

<syncfusion:SfDataGrid Name="datagrid"
                               AutoGenerateRelations="False"
                               ItemsSource="{Binding DataCollection}">
    <syncfusion:SfDataGrid.DetailsViewDefinition>
        <syncfusion:GridViewDefinition RelationalColumn="Supplier_Product">
            <syncfusion:GridViewDefinition.DataGrid>
                <syncfusion:SfDataGrid x:Name="FirstLevelNestedGrid" AutoGenerateColumns="True" />
            </syncfusion:GridViewDefinition.DataGrid>
        </syncfusion:GridViewDefinition>
    </syncfusion:SfDataGrid.DetailsViewDefinition>
</syncfusion:SfDataGrid>


``` 

![DetailsView_DataTable](Images/DetailsView_DataTable.png)

## Expanding and collapsing the DetailsViewDataGrid programmatically
SfDataGrid allows you to expand or collapse the DetailsViewDataGrid programmatically in different ways.

### Expand or collapse all the DetailsViewDataGrid
You can expand or collapse all the DetailsViewDataGrid programmatically by using [ExpandAllDetailsView](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html#Syncfusion_UI_Xaml_Grid_SfDataGrid_ExpandAllDetailsView) and [CollapseAllDetailsView](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html#Syncfusion_UI_Xaml_Grid_SfDataGrid_CollapseAllDetailsView) methods.

``` c#
this.dataGrid.ExpandAllDetailsView();
this.dataGrid.CollapseAllDetailsView();
```

### Expand DetailsViewDataGrid based on level

You can expand all the DetailsViewDataGrid programmatically based on level using [ExpandAllDetailsView](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html#Syncfusion_UI_Xaml_Grid_SfDataGrid_ExpandAllDetailsView_System_Int32_) method.

``` c#
this.dataGrid.ExpandAllDetailsView(2);
```

### Expand or collapse Details View based on record index

You can expand or collapse DetailsViewDataGrid based on the record index by using [ExpandDetailsViewAt](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html#Syncfusion_UI_Xaml_Grid_SfDataGrid_ExpandDetailsViewAt_System_Int32_) and [CollapseDetailsViewAt](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html#Syncfusion_UI_Xaml_Grid_SfDataGrid_CollapseDetailsViewAt_System_Int32_) methods.

``` c#
this.dataGrid.ExpandDetailsViewAt(0);
this.dataGrid.CollapseDetailsViewAt(0);
```

## Defining properties for DetailsViewDataGrid When AutoGenerateRelations is false

For manually defined relation, the properties can be directly set to the [GridViewDefinition.DataGrid](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.GridViewDefinition.html#Syncfusion_UI_Xaml_Grid_GridViewDefinition_DataGrid).

``` Xaml
<syncfusion:SfDataGrid x:Name="dataGrid"
                       AutoGenerateColumns="True"
                       AutoGenerateRelations="False"
                       ItemsSource="{Binding Orders}">
            <syncfusion:SfDataGrid.DetailsViewDefinition>
                <syncfusion:GridViewDefinition RelationalColumn="ProductDetails">
                    <syncfusion:GridViewDefinition.DataGrid>
                        <syncfusion:SfDataGrid x:Name="FirstLevelNestedGrid"
                                               AllowEditing="True"
                                               AllowFiltering="True"
                                               AllowResizingColumns="True"
                                               AllowSorting="True"
                                               AutoGenerateColumns="False" />
                    </syncfusion:GridViewDefinition.DataGrid>
                </syncfusion:GridViewDefinition>
            </syncfusion:SfDataGrid.DetailsViewDefinition>
</syncfusion:SfDataGrid>

```

## Defining properties for DetailsViewDataGrid When AutoGenerateRelations is true

When the relation is auto-generated, you can get the GridViewDefinition.DataGrid in the [AutoGeneratingRelations](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html) event handler to set the properties.

``` c#

this.dataGrid.AutoGeneratingRelations += dataGrid_AutoGeneratingRelations;

void dataGrid_AutoGeneratingRelations(object sender, Syncfusion.UI.Xaml.Grid.AutoGeneratingRelationsArgs e)
{
     e.GridViewDefinition.DataGrid.AllowEditing = true;
     e.GridViewDefinition.DataGrid.AllowFiltering = true;
     e.GridViewDefinition.DataGrid.AllowSorting = true;
     e.GridViewDefinition.DataGrid.AllowResizingColumns = true;        
}

```


## Hiding expander when parent record’s relation property has an empty collection or null

By default, the expander will be visible for all the data rows in parent DataGrid even if its RelationalColumn property has an empty collection or null.
You can hide the expander from the view when corresponding RelationalColumn property has an empty collection or null, by setting [HideEmptyGridViewDefinition](https://help.syncfusion.com/cr/wpf/Syncfusion.UI.Xaml.Grid.SfDataGrid.html#Syncfusion_UI_Xaml_Grid_SfDataGrid_HideEmptyGridViewDefinition) property as true.

``` Xaml
<syncfusion:SfDataGrid x:Name="dataGrid" 
                               HideEmptyGridViewDefinition="True"
                               ItemsSource="{Binding Path=OrdersDetails}">
```


![DetailsView_HideEmptyGridViewDefinition](Images/DetailsView_HideEmptyGridViewDefinition.png)


## Requirements to run the demo 

Visual Studio 2015 and above versions.
