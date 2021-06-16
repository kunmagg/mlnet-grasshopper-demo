
This guide shows you how to train a model in [ML.NET](http://ml.NET) and create a Grasshopper component that uses said model. In this example, we predict the square root of a number using the `sqrt_dataset.csv`-file that contains 1000000 real numbers between 0-1000 and their square root. For installation and setup of ML.NET, follow the official Microsoft guide: [https://dotnet.microsoft.com/learn/ml-dotnet/get-started-tutorial/intro](https://dotnet.microsoft.com/learn/ml-dotnet/get-started-tutorial/intro) 

Here, *Project*, refers to the name of the project you want to add [ML.NET](http://ml.NET) to.

# 1. Create [ML.NET](http://ml.NET) model in GH project.

- Right-click the GH project you want to add a [ML.NET](http://ml.NET) component to → Add → Machine Learning.
- Select the *value regression* scenario.
- Upload the `sqrt_dataset.csv` and select the column titled *label* as your label. Use the recommended training time and Start training.
- After training is completed, two projects get added to your solution: `ProjectML.ConsoleApp` and `ProjectML.Model`.
    - Delete ConsoleApp.

# 2. Tweak the config to add Model-project to main project.

**Add dependency to Project from ProjectML.Model**

- Right-click Solution → Properties
    - Project dependencies:
        - Projects: `Project` → Depends on: ...Model

**Add project reference from Project to ProjectML.Model**

- Right-click `Project` → Unload project.
- Right-click unloaded `Project` → Edit project file.
- Above the first `ItemGroup`, cirka line 36, add:

    ```xml
    <ItemGroup>
      <ProjectReference Include="" />
    </ItemGroup>
    ```

- Right-click `ProjectML.Model` → Copy full path.
- Inside Include, paste the full path, e.g.:

    ```xml
    <ItemGroup>
      <ProjectReference Include="C:\Users\...\Project\ProjectML.Model\ProjectML.Model.csproj" />
    </ItemGroup>
    ```

- Remove everything before (and including the Project-directory):

    ```xml
    <ItemGroup>
      <ProjectReference Include="ProjectML.Model\ProjectML.Model.csproj" />
    </ItemGroup>
    ```

- Right-click `Project` → Reload project with dependencies.

# 3. Create GH-component

- Create GH-component as usual.
- Inside the new GH-component, add:

```csharp
using ProjectML.Model;
```

- To use model, create new `ModelInput` and get the `Score` property of `Predict`-method output of `ConsumeModel` class like:

```csharp
ModelInput modelData = new ModelInput()
{
    Number = Convert.ToSingle(numberToSquareRoot)
};
float rootedSingle = ConsumeModel.Predict(modelData).Score;
```

Regression input and output is by default a **float**. Use Convert.ToSingle to convert double to float.

## Congrats, you now have a machine learning component that kinda predicts the square root of a number!
