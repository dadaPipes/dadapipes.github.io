# Blazor

## Intro

Jeg havde valgt Blazor Standalone fordi at jeg var sikker på at det var det eneste der understøttede [PWA](/docs/TechRamme/Blazor.html?tabs=progressive-web-app#tabpanel_1_progressive-web-app), hvilket var et vigtigt krav til applikationen.
Men det viste sig senere, efter at have udviklet den første Epic, at det ikke kun var Standalone der understøttede [PWA](/docs/TechRamme/Blazor.html?tabs=progressive-web-app#tabpanel_1_progressive-web-app).

Det var en dyr fejl, da det gjorde mange ting meget mere besværligt.
Af skade bliver man klog, men lige det her kunne jeg godt have været foruden.

Da Blazor Standalone, som med React, Vue og Angular, *ikke* kræver en .NET server til at serve UI'en, er der ikke helt så meget der støtter Blazor Standalone, som fx Blazor Web App, der kræver en .NET server for at serve UI'en.

Oven i hatten, så er Blazor som et front end framework, der ikke er afhængig af en bestemt form for server, ikke så populært som fx React, Angular og Vue. Derfor eksisterer der heller ikke dokumentation for Blazor Standalone, på lige fod med de Java Script baserede frameworks.

På den ene side ville det have været meget nemmere, hvis jeg havde valgt Blazor Web App, hvor jeg bare kunne scaffolde eksempelvis, Docker og Identity i applikationen, så det havde virket på magisk vis, hvor at Client og Server spiller sammen fra start.
På den anden side, så fik jeg lært noget om hvordan tingene fungere bag om det magiske tæppe.

**Lad os starte hårdt ud fra toppen**  

### Main Layout

```csharp
@using Microsoft.AspNetCore.Components.Routing
@using MudBlazor
@inherits LayoutComponentBase

<MudThemeProvider />
<MudPopoverProvider />
<MudDialogProvider />
<MudSnackbarProvider />

<MudLayout>
    <MudAppBar Elevation="1">
        <AuthorizeView Roles="Admin">
            <MudButton Variant="Variant.Filled"
                       Color="Color.Secondary" 
                       OnClick="@ToggleDrawer">
                       Admin</MudButton>
        </AuthorizeView>

        <MudNavLink Href="/" Match="NavLinkMatch.All">
            <MudButton Variant="Variant.Filled" 
                       Color="Color.Primary">
                       Home</MudButton>
        </MudNavLink>

        <AuthorizeView Roles="Admin, Judge, DogHandler">
            <Authorized>
                <MudNavLink Href="/logout" Match="NavLinkMatch.All">
                    <MudButton Variant="Variant.Filled" 
                               Color="Color.Primary">
                               Logout</MudButton>
                </MudNavLink>
            </Authorized>
            <NotAuthorized>
                <MudNavLink Href="/login">
                    <MudButton Variant="Variant.Filled" 
                               Color="Color.Primary">
                               Login</MudButton>
                </MudNavLink>
            </NotAuthorized>
        </AuthorizeView>
    </MudAppBar>

    <MudDrawer @bind-Open="@_open" 
               ClipMode="DrawerClipMode.Always" 
               Elevation="2" 
               Variant="@DrawerVariant.Temporary" 
               Color="Color.Primary">
        
        <MudDrawerHeader>
            <MudText Typo="Typo.h6">Admin</MudText>
        </MudDrawerHeader>

        <MudNavMenu>
            <MudNavLink Match="NavLinkMatch.All" 
                        Href="/admin/guide" 
                        Icon="@Icons.Material.Filled.People" 
                        IconColor="Color.Inherit">
                        Admin Guide</MudNavLink>
            
            <MudNavLink Match="NavLinkMatch.All" 
                        Href="/admin/users" 
                        Icon="@Icons.Material.Filled.LocalLibrary" 
                        IconColor="Color.Inherit">
                        Manage Users</MudNavLink>

            <MudNavGroup Title="Edit Template" 
                         Icon=@Icons.Material.Filled.AddModerator 
                         IconColor="Color.Inherit" 
                         Expanded="false">

                <MudNavLink Match="NavLinkMatch.All" 
                            Href="/admin/template">
                            Rallyfield Templates</MudNavLink>

                <MudNavLink Match="NavLinkMatch.All" 
                            Href="/admin/rallyfield-rules">
                            Rallyfield Rules</MudNavLink>

            </MudNavGroup>

            <MudNavLink Match="NavLinkMatch.All" 
                        Href="/admin/claims" 
                        Icon=@Icons.Material.Filled.AddModerator 
                        IconColor="Color.Inherit">
                        Claims</MudNavLink>

        </MudNavMenu>
    </MudDrawer>

    <MudMainContent>
        @Body
    </MudMainContent>
</MudLayout>

@code {
    private bool _open = false;

    private void ToggleDrawer()
    {
        _open = !_open;
    }
}
```

### ExerciseForm.cs

```csharp
@inject ImageProcessingService ImageService
@inject ExerciseService ExerciseService
@inject TypeService TypeService
@inject TempoService TempoService
@inject ObstacleService ObstacleService;

<MudForm Model="exercise" 
         @ref="form" 
         Validation="@(validator.ValidateValue)" 
         ValidationDelay="0">

    <MudTextField @bind-Value="exercise.Number"
                  For="@(() => exercise.Number)"
                  Immediate="true"
                  Label="Number"
                  Variant="Variant.Outlined"
                  Class="mx-4" />

    <MudTextField @bind-Value="exercise.Name"
                  For="@(() => exercise.Name)"
                  Immediate="true"
                  Label="Name*"
                  Variant="Variant.Outlined"
                  Class="mx-4" />

    <MudTextField @bind-Value="exercise.Description"
                  For="@(() => exercise.Description)"
                  Immediate="true"
                  Label="Description"
                  Lines="5"
                  Variant="Variant.Outlined"
                  Class="mx-4" />

    <MudField Class="ma-4" Variant="Variant.Outlined">

        <MudText Typo="Typo.h6" Class="ma-4">Side Handling</MudText>

        <MudCheckBox @ref="LeftHandledCheckbox"
                     @bind-Value="exercise.PositionLeft"
                     For="@(() => exercise.PositionLeft)"
                     Label="Left"
                     Class="mx-4" />

        <MudCheckBox @ref="RightHandledCheckbox"
                     @bind-Value="exercise.PositionRight"
                     For="@(() => exercise.PositionRight)"
                     Label="Right"
                     Class="mx-4" />
    </MudField>

    <MudSelect @ref="levelSelect"
               T="GetLevelTemplate" 
               @bind-Value="selectedLevel"
               ToStringFunc="@(l => l.Name)"
               Variant="Variant.Outlined"
               MultiSelection="false"
               Label="Select level"
               Class="ma-4">

        @foreach (var level in levels)
        {
            <MudSelectItem T="GetLevelTemplate" Value="@level">
                @level.Name
            </MudSelectItem>
        }
    </MudSelect>

    <MudSelect @ref="typeSelect"
               @bind-SelectedValues="selectedTypes"
               ToStringFunc="@(t => t.Name)"
               Variant="Variant.Outlined"
               MultiSelection="true"
               Label="Select exercise types"
               Class="ma-4">

        @foreach (var type in types)
        {
            <MudSelectItem T="GetTypeTemplate" Value="@type">
                @type.Name
            </MudSelectItem>
        }
    </MudSelect>

    <MudSelect @ref="tempoSelect"
               @bind-SelectedValues="selectedTempos"
               ToStringFunc="@(t => t.Name)"
               Variant="Variant.Outlined"
               MultiSelection="true"
               Label="Select exercise tempos"
               Class="ma-4">

        @foreach (var tempo in tempos)
        {
            <MudSelectItem T="GetTempoTemplate" Value="@tempo">
                @tempo.Name
            </MudSelectItem>
        }
    </MudSelect>

    <MudSelect @ref="obstacleSelect"
               @bind-SelectedValues="selectedObstacles"
               ToStringFunc="@(t => t.Name)"
               Variant="Variant.Outlined"
               MultiSelection="true"
               Label="Select obstacles"
               Class="ma-4">

        @foreach (var obstacle in obstacles)
        {
            <MudSelectItem T="GetObstacleTemplate" Value="@obstacle">
                @obstacle.Name
            </MudSelectItem>
        }
    </MudSelect>

    <MudField Class="ma-4" Variant="Variant.Outlined">
        <MudStack Class="pa-4">

            @if (!string.IsNullOrEmpty(b64))
            {
                <MudImage Src="@b64" Alt="Uploaded Image" ObjectFit="ObjectFit.Contain" Width="100" />
            }

            <MudFileUpload @ref="@imageUpload"
                           T="IBrowserFile"
                           @bind-Files="uploadedImage"
                           Accept=".png, .jpg, .jpeg"
                           OnFilesChanged="UploadImageToFormAsync"
                           MaximumFileCount="1">

                <ActivatorContent>
                    <MudButton Variant="Variant.Filled"
                               Color="Color.Primary"
                               StartIcon="@Icons.Material.Filled.CloudUpload">
                        Upload image file
                    </MudButton>
                </ActivatorContent>

            </MudFileUpload>

        </MudStack>
    </MudField>

    <MudButton Variant="Variant.Filled"
               Color="Color.Primary"
               Class="ml-auto"
               OnClick="@(async () => await Submit())">
        Save
    </MudButton>

</MudForm>

@code {
    [Inject] ISnackbar snackbar { get; set; }

    MudForm form                                  = new();
    MudCheckBox<bool> LeftHandledCheckbox         = new();
    MudCheckBox<bool> RightHandledCheckbox        = new();
    MudSelect<GetLevelTemplate> levelSelect       = new();
    MudSelect<GetTypeTemplate> typeSelect         = new();
    MudSelect<GetTempoTemplate> tempoSelect       = new();
    MudSelect<GetObstacleTemplate> obstacleSelect = new();
    IBrowserFile? uploadedImage;
    MudFileUpload<IBrowserFile>? imageUpload;

    string b64;
    CreateExerciseTemplate exercise           = new();
    CreateExerciseTemplateValidator validator = new();

    GetLevelTemplate selectedLevel = new();
    List<GetLevelTemplate> levels = [];

    IEnumerable<GetTypeTemplate> selectedTypes                 = [];
    public List<GetTypeTemplate> types                         = [];
    IEnumerable<GetTempoTemplate> selectedTempos               = [];
    public  List <GetTempoTemplate> tempos                     = [];
    IEnumerable<GetObstacleTemplate> selectedObstacles         = [];
    public List<GetObstacleTemplate> obstacles                 = [];

    [Parameter] public EventCallback<GetExerciseTemplate> OnExerciseAdded { get; set; }

    public void LoadLevels(List<GetLevelTemplate> levels)
    {
        this.levels = levels;
    }
    
    public void LoadPartialTemplateData(List<GetLevelTemplate> levels, List<GetTypeTemplate> types, List<GetTempoTemplate> tempos, List<GetObstacleTemplate> obstacles)
    {
        this.levels    = levels;
        this.types     = types;
        this.tempos    = tempos;
        this.obstacles = obstacles;
        //StateHasChanged();
    }
    
    public void AddNewLevel(GetLevelTemplate level)
    {
        levels.Add(level);
        StateHasChanged();
    }

    public void AddNewExerciseType(GetTypeTemplate type)
    {
        types.Add(type);
        StateHasChanged();
    }

    public void AddNewExerciseTempo(GetTempoTemplate tempo)
    {
        tempos.Add(tempo);
        StateHasChanged();
    }

    public void AddNewObstacle(GetObstacleTemplate obstacle)
    {
        obstacles.Add(obstacle);
        StateHasChanged();
    }

    public void UpdateLevel(GetLevelTemplate updatedLevel)
    {
        var levelToRemove = levels.Find(t => t.Id == updatedLevel.Id);

        if (levelToRemove is not null)
        {
            levels.Remove(levelToRemove);
            levels.Add(updatedLevel);
            typeSelect.Clear();
            StateHasChanged();
        }
        else
        {
            snackbar.Add("Could not find type", Severity.Error);
            Console.WriteLine("Could not find type to remove before update.");
            return;
        }
    }

    public void UpdateExerciseType(GetTypeTemplate updatedType)
    {
        var typeToRemove = types.Find(t => t.Id == updatedType.Id);

        if (typeToRemove is not null)
        {
            types.Remove(typeToRemove);
            types.Add(updatedType);
            typeSelect.Clear();
            StateHasChanged();
        }
        else
        {
            snackbar.Add("Could not find type", Severity.Error);
            Console.WriteLine("Could not find type to remove before update.");
            return;
        }
    }

    public void UpdateExerciseTempo(GetTempoTemplate updatedTempo)
    {
        var tempoToRemove = tempos.Find(t => t.Id == updatedTempo.Id);

        if (tempoToRemove is not null)
        {
            tempos.Remove(tempoToRemove);
            tempos.Add(updatedTempo);
            tempoSelect.Clear();
            StateHasChanged();
        }
        else
        {
            snackbar.Add("Could not find tempo", Severity.Error);
            Console.WriteLine("Could not find tempo to remove before update.");
            return;
        }
    }

    public void UpdateObstacle(GetObstacleTemplate updatedObstacle)
    {
        var obstacleToRemove = obstacles.Find(t => t.Id == updatedObstacle.Id);

        if (obstacleToRemove is not null)
        {
            obstacles.Remove(obstacleToRemove);
            obstacles.Add(updatedObstacle);
            obstacleSelect.Clear();
            StateHasChanged();
        }
        else
        {
            snackbar.Add("Could not find type", Severity.Error);
            Console.WriteLine("Could not find type to remove before update.");
            return;
        }
    }

    public void RemoveExerciseTypeTemplate(GetTypeTemplate type)
    {
        types.Remove(type);
        typeSelect.Clear();
        StateHasChanged();
    }

    public void RemoveTempoTemplate(GetTempoTemplate tempo)
    {
        tempos.Remove(tempo);
        tempoSelect.Clear();
        StateHasChanged();
    }

    public void RemoveObstacle(GetObstacleTemplate obstacle)
    {
        obstacles.Remove(obstacle);
        obstacleSelect.Clear();
        StateHasChanged();
    }

    
    async Task Submit()
    {
        await form.Validate();
        if (!form.IsValid)
        {
            snackbar.Add("Invalid form", Severity.Error);
            return;
        }
        try
        {
            var createdExercise = await ExerciseService.CreateExerciseAsync(exercise, selectedLevel, selectedTypes, selectedTempos, selectedObstacles, uploadedImage);

            await form.ResetAsync();
            exercise.PositionLeft = false;
            exercise.PositionRight = false;
            await imageUpload.ClearAsync();
            b64 = string.Empty;
            await typeSelect.Clear();
            await tempoSelect.Clear();
            await obstacleSelect.Clear();

            await OnExerciseAdded.InvokeAsync(createdExercise);
            snackbar.Add("Exercise succesfully Saved.", Severity.Success);
        }
        catch (HttpRequestException ex)
        {
            snackbar.Add("An error occurred while saving. Please check your network connection.", Severity.Error);
            Console.WriteLine($"HTTP error: {ex.Message}");
        }
        catch (InvalidOperationException ex)
        {
            snackbar.Add("An error occurred while processing the response from the server.", Severity.Error);
            Console.WriteLine($"Invalid operation: {ex.Message}");
        }
        catch (Exception ex)
        {
            snackbar.Add("An unexpected error occurred while saving the exercice template.", Severity.Error);
            Console.WriteLine($"Unexpected error: {ex.Message}");
        }
    }

    async Task UploadImageToFormAsync()
    {
        var fileSizeBytes = uploadedImage.Size;

        try
        {
            // Log the initial file size
            Console.WriteLine($"Initial file size: {fileSizeBytes} bytes");

            if (fileSizeBytes >= 512000) // Max size for Base64 conversion
            {
                Console.WriteLine($"File size exceeds {512000 / 1024 / 1024:F2} MB limit");
                snackbar.Add("Max file size for Base64 conversion is 0.5 MB", Severity.Warning);
            }

            b64 = await ImageService.ConvertToBase64Async(uploadedImage);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error during upload: {ex.Message}");
            snackbar.Add("An error occurred doing image upload", Severity.Error);
        }
    }
}
```

De forskellige dele af en exercise, består af mindre dele som en bruger har valgt i de forudgående forms.
Jeg har samlet variablerne i ```LoadPartialTemplateData()``` som argumenter, for at kunne samle dem,
og lavet den public, så den kan modtage data fra andre steder i applikationen og opdatere komponenten.

[FluentValidation](https://docs.fluentvalidation.net/en/latest/) .NET bibliotek for validering.
Det er et meget populært og udbredt, men Blazor understøtter det ikke direkte, da det primært er for Server side.
Da en af hovedårsagerne til at jeg valgte Blazor til min .NET API, var at jeg kunne bruge C# klasser eller records til DTO's, så jeg kunne bruge de samme objekter i både front og backend.
Ved at bruge de samme objekter til at transportere data mellem front og backend, kan jeg bruge den samme validerings kode i begge ender, i stedet for at holde styr på validering begge steder, eller droppe validering i front end, da det ville være nemmere at holde styr på og det vigtigste at sikre er serveren.

Med [MudBlazor](https://mudblazor.com/components/form#using-fluent-validation) kan jeg bruge Fluent validation i mine DTO's og bruge de DTO's i både Blazor components og i mine endpoints og dermed nå frem til den samme validering i både front- og backend.

### CreateExerciseTemplate

```exercise``` som er den model jeg bruger for exerciseTemplate formen er en implementering af ```CreateExerciseTemplate```.
```validator``` er en implementering af ```CreateExerciseTemplateValidator``` der validerer ```CreateExerciseTemplate```

```csharp
namespace DogObedience.Shared.Admin.Templates.ExerciseTemplate;

public class CreateExerciseTemplate
{
    public bool IsTemplate { get; set; } = true;
    public int Number { get; set; }
    public string Name { get; set; } = string.Empty;
    public string Description { get; set; } = string.Empty;
    public bool PositionLeft { get; set; }
    public bool PositionRight { get; set; }
    public string Color { get; set; } = string.Empty;
    public int LevelId { get; set; }
    public ICollection<CreateTypeTemplate> Types { get; set; } = [];
    public ICollection<CreateTempoTemplate> Tempos { get; set; } = [];
    public ICollection<CreateObstacleTemplate> Obstacles { get; set; } = [];
    public IFormFile? ImageFile { get; set; }
}

public class CreateExerciseTemplateValidator : AbstractValidator<CreateExerciseTemplate>
{
    public CreateExerciseTemplateValidator()
    {
        RuleFor(x => x.Number).NotEmpty();
        RuleFor(x => x.Name).NotEmpty();
        RuleFor(x => x.Description).NotEmpty();
        RuleFor(x => x.ImageFile.Length).LessThanOrEqualTo(512000); // Max size for Base64 conversion
        RuleFor(x => x.Types).NotEmpty();
        RuleFor(x => x.Tempos).NotEmpty();
        RuleFor(x => x.Obstacles).NotEmpty();
    }

    public Func<object, string, Task<IEnumerable<string>>> ValidateValue => async (model, propertyName) =>
    {
        var result = await ValidateAsync(ValidationContext<CreateExerciseTemplate>.CreateWithOptions((CreateExerciseTemplate)model, x => x.IncludeProperties(propertyName)));
        if (result.IsValid)
            return Array.Empty<string>();
        return result.Errors.Select(e => e.ErrorMessage);
    };
}
```

### ExerciseDataGrid

Er af tilsvarende kompleksitet som ExerciseForm

```csharp

@inject ExerciseService ExerciseService 
@inject TypeService TypeService
@inject TempoService TempoService
@inject ObstacleService ObstacleService;
@inject ImageProcessingService ImageService

@if (exercises == null || exercises.Count() == 0)
{
    <p>No exercise created yet.</p>
}

<MudDataGrid T="GetExerciseTemplate" 
             Items="@exercises" 
             ReadOnly="@false" 
             EditMode="@DataGridEditMode.Form"
             Bordered="true" 
             Dense="true" 
             Hover="true"
             EditTrigger="@DataGridEditTrigger.OnRowClick"
             CommittedItemChanges="@CommittedItemChanges">

    <Columns>
        <PropertyColumn Property="x => x.Number" />
        <PropertyColumn Property="x => x.Name" />
        <PropertyColumn Property="x => x.Description" />

        <PropertyColumn Property="x => x.Types">
            <EditTemplate>
                <MudSelect T="GetTypeTemplate"
                           @bind-SelectedValues="@context.Item.Types"
                           ToStringFunc="@(t => t.Name)"
                           MultiSelection="true">

                    @foreach (var type in types)
                    {
                    <MudSelectItem T="GetTypeTemplate">
                            @type.Name
                        </MudSelectItem>
                    }

                </MudSelect>
            </EditTemplate>
            <CellTemplate>
                @string.Join(", ", context.Item.Types.Select(x => x.Name))
            </CellTemplate>
        </PropertyColumn>

        <PropertyColumn Property="x => x.Tempos">
            <EditTemplate>
                <MudSelect T="GetTempoTemplate"
                           @bind-SelectedValues="@context.Item.Tempos"
                           ToStringFunc="@(t => t.Name)"
                           MultiSelection="true">

                    @foreach (var tempo in tempos)
                    {
                    <MudSelectItem T="GetTempoTemplate">
                            @tempo.Name
                        </MudSelectItem>
                    }

                </MudSelect>
            </EditTemplate>
            <CellTemplate>
                @string.Join(", ", context.Item.Tempos.Select(x => x.Name))
            </CellTemplate>
        </PropertyColumn>

        <PropertyColumn Property="x => x.PositionLeft" Title="Left Handled">
            <EditTemplate>
                <MudCheckBox @bind-Value="context.Item.PositionLeft" Label="Left"/>
            </EditTemplate>
        </PropertyColumn>

        <PropertyColumn Property="x => x.PositionRight" Title="Right Handled">
            <EditTemplate>
                <MudCheckBox @bind-Value="context.Item.PositionRight" Label="Right"/>
            </EditTemplate>
        </PropertyColumn>

        <PropertyColumn Property="x => x.Obstacles">
            <EditTemplate>
                <MudSelect T="GetObstacleTemplate"
                           @bind-SelectedValues="@context.Item.Obstacles"
                           ToStringFunc="@(o => o.Name)"
                           MultiSelection="false">

                    @foreach (var obstacle in obstacles)
                    {
                        <MudSelectItem T="GetObstacleTemplate">
                            @obstacle.Name
                        </MudSelectItem>
                    }

                </MudSelect>
            </EditTemplate>
            <CellTemplate>
                <MudStack Row="true" Class="d-flex align-center">
                    
                    <MudText>@string.Join(", ", context.Item.Obstacles.Select(x => x.Name))</MudText>
                    
                    @if (context.Item.B64 is not null)
                    {
                        <MudImage Src="@context.Item.B64"
                                  Alt="Exercise Image"
                                  ObjectFit="ObjectFit.Contain"
                                  Width="100" />
                    }
                    else
                    {
                        <p>No image available</p>
                    }

                </MudStack>
            </CellTemplate>
        </PropertyColumn>

        <PropertyColumn Property="x => x.ImageFile" Title="Image">
            <EditTemplate>
                <MudFileUpload @ref="@imageUpload"
                               T="IBrowserFile"
                               @bind-Files="@context.Item.ImageFile"
                               Accept=".png, .jpg, .jpeg"
                               OnFilesChanged="async (e) => await UploadImageToDataGridAsync(context.Item.ImageFile)"
                               MaximumFileCount="1">
                    <ActivatorContent>
                        <MudButton Variant="Variant.Filled"
                                   Color="Color.Primary"
                                   StartIcon="@Icons.Material.Filled.CloudUpload">
                            Upload image file
                        </MudButton>
                    </ActivatorContent>
                </MudFileUpload>
            </EditTemplate>
            <CellTemplate>
                @if (context.Item.B64 is not null)
                {
                    <MudImage Src="@context.Item.B64"
                              Alt="Exercise Image" 
                              ObjectFit="ObjectFit.Contain" 
                              Width="100" />
                }
                else
                {
                    <p>No image available</p>
                }
            </CellTemplate>
        </PropertyColumn>

        <TemplateColumn CellClass="d-flex justify-end">
            <CellTemplate>
                <MudIconButton 
                    Size="@Size.Medium" 
                    Icon="@Icons.Material.Filled.Delete" 
                    Color="Color.Error"
                    OnClick="async (e) => await DeleteExerciseAsync(context.Item.Id)" />
            </CellTemplate>
        </TemplateColumn>

    </Columns>
</MudDataGrid>

@code {

    [Inject] ISnackbar snackbar { get; set; }

    string dataGridB64 = string.Empty;
    IBrowserFile image;
    MudFileUpload<IBrowserFile> imageUpload;
    List<GetLevelTemplate> levels        = [];
    List<GetExerciseTemplate> exercises  = [];
    List<GetTypeTemplate> types          = [];
    List<GetTempoTemplate> tempos        = [];
    List<GetObstacleTemplate> obstacles  = [];

    public void LoadExerciseTemplates(List<GetExerciseTemplate> exercises)
    {
        this.exercises = exercises;
        StateHasChanged();
    }

    public void LoadPartialTemplateData(List<GetLevelTemplate> levels, List<GetTypeTemplate> types, List<GetTempoTemplate> tempos, List<GetObstacleTemplate> obstacles)
    {
        this.levels    = levels;
        this.types     = types;
        this.tempos    = tempos;
        this.obstacles = obstacles;
        StateHasChanged();
    }

    public void AddNewExercise(GetExerciseTemplate newExerciseType)
    {
        exercises.Add(newExerciseType);
        StateHasChanged();
    }

    async Task CommittedItemChanges(GetExerciseTemplate exercise)
    {
        try
        {
            var updatedExercise = await ExerciseService.UpdateExerciseTemplateAsync(exercise);

            var exerciseToRemove = exercises.Find(e => e.Id == exercise.Id);
            
            if(exercise is not null)
            {
                exercises.Remove(exerciseToRemove);
                exercises.Add(updatedExercise);
            }

            StateHasChanged();
            snackbar.Add("Exercise succesfully updated", Severity.Success);
        }
        catch (HttpRequestException ex)
        {
            snackbar.Add("An error occurred while saving. Please check your network connection.", Severity.Error);
            Console.WriteLine($"HTTP error: {ex.Message}");
        }
        catch (InvalidOperationException ex)
        {
            snackbar.Add("An error occurred while processing the response from the server.", Severity.Error);
            Console.WriteLine($"Invalid operation: {ex.Message}");
        }
        catch (Exception ex)
        {
            snackbar.Add("An unexpected error occurred while updating exercise template.", Severity.Error);
            Console.WriteLine($"Unexpected error: {ex.Message}");
        }
    }

    async Task DeleteExerciseAsync(int? id)
    {
        try
        {
            await ExerciseService.DeleteExerciseTemplateAsync(id);
            var exerciseToRemove = exercises.Find(p => p.Id == id);
            if (exerciseToRemove is not null)
            {
                exercises.Remove(exerciseToRemove);
                snackbar.Add("Exercise template successfully deleted.", Severity.Success);
            }
            else
            {
                snackbar.Add("No exercise template found with the given ID.", Severity.Warning);
            }
        }
        catch (HttpRequestException ex)
        {
            snackbar.Add("An error occurred while saving. Please check your network connection.", Severity.Error);
            Console.WriteLine($"HTTP error: {ex.Message}");
        }
        catch (InvalidOperationException ex)
        {
            snackbar.Add("An error occurred while processing the response from the server.", Severity.Error);
            Console.WriteLine($"Invalid operation: {ex.Message}");
        }
        catch (Exception ex)
        {
            snackbar.Add("An unexpected error occurred while deleting exercise templates.", Severity.Error);
            Console.WriteLine($"Unexpected error: {ex.Message}");
        }
    }

    async Task UploadImageToDataGridAsync(IBrowserFile image) => dataGridB64 = await ImageService.ConvertToBase64Async(image);
}
```

### ExerciseService

Har ansvaret for at send og modtage data fra Serveren.
Da jeg ikke kunne sende en fil som en del af et C# object, men stadig skulle sende 1 object til serveren, endte det med
at jeg måtte bruge et [multipartformdatacontent](https://learn.microsoft.com/en-us/dotnet/api/system.net.http.multipartformdatacontent?view=net-8.0) og så mappe hver property til en navngiven multipartformdata property.
Jeg skulle så samtidig sørge for at det endpoint jeg sender multipartformdata objektet til, er sat op så at det kan modtage et objekt der har de samme property navne og variabler.

Hovedårsagen til denne kompleksitet er at man ikke kan stole på hvad som bliver uploaded på Client side.
Havde koden været på serveren, havde den været mere sikker, og jeg ville kunne scanne den for virus og sende den direkte til en database eller et [blob storage](https://learn.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction).

```csharp
namespace DogObedience.Client.Features.Admin.Templates.ExerciseTemplate;

public class ExerciseService
{
    private readonly HttpClient _httpClient;
    private readonly ImageProcessingService _imageProcessingService;

    public ExerciseService(HttpClient httpClient, ImageProcessingService imageProcessingService)
    {
        _httpClient = httpClient;
        _imageProcessingService = imageProcessingService;
    }

    public async Task<GetExerciseTemplate> CreateExerciseAsync(
        CreateExerciseTemplate exercise, 
        GetLevelTemplate selectedLevel, 
        IEnumerable<GetTypeTemplate> selectedTypes,
        IEnumerable<GetTempoTemplate> selectedTempos,
        IEnumerable<GetObstacleTemplate> selectedObstacles,
        IBrowserFile imageFile)
    {
        try
        {
            var content = new MultipartFormDataContent
            {
                // Exercise template data
                { new StringContent(exercise.Number.ToString()), "Number" },
                { new StringContent(exercise.Name), "Name" },
                { new StringContent(exercise.Description), "Description" },
                { new StringContent(exercise.PositionLeft.ToString()), "PositionLeft" },
                { new StringContent(exercise.PositionRight.ToString()), "PositionRight" },

                // Level template data
                { new StringContent(selectedLevel.Id.ToString()), "LevelId" },
                { new StringContent(selectedLevel.Name), "Level.Name" },
                { new StringContent(selectedLevel.Color), "Color" },
            };
            
            // Types
            int typeIndex = 0;
            foreach (var type in selectedTypes)
            {
                content.Add(new StringContent(type.IsTemplate.ToString()), $"Types[{typeIndex}].IsTemplate");
                content.Add(new StringContent(type.Name), $"Types[{typeIndex}].Name");
                typeIndex++;
            }
            
            // Tempos
            int tempoIndex = 0;
            foreach (var tempo in selectedTempos)
            {
                content.Add(new StringContent(tempo.IsTemplate.ToString()), $"Tempos[{tempoIndex}].IsTemplate");
                content.Add(new StringContent(tempo.Name), $"Tempos[{tempoIndex}].Name");
                tempoIndex++;
            }
            
            // Obstacles
            int obstacleIndex = 0;
            foreach (var obstacle in selectedObstacles)
            {
                content.Add(new StringContent(obstacle.IsTemplate.ToString()), $"Obstacles[{obstacleIndex}].IsTemplate");
                content.Add(new StringContent(obstacle.Name), $"Obstacles[{obstacleIndex}].Name");
                content.Add(new StringContent(obstacle.B64), $"Obstacles[{obstacleIndex}].B64");
                obstacleIndex++;
            }
            
            // ImageFile
            await using var fileStream = imageFile.OpenReadStream();
            using var streamContent = new StreamContent(fileStream);
            streamContent.Headers.ContentType = new MediaTypeHeaderValue(imageFile.ContentType);
            
            content.Add(streamContent, "ImageFile", imageFile.Name);
            
            var response = await _httpClient.PostAsync("api/admin/field-template/level-template/exercise-template", content);
            if (!response.IsSuccessStatusCode)
            {
                var errorContent = await response.Content.ReadAsStringAsync();
                throw new HttpRequestException($"Request failed with status code {response.StatusCode}: {errorContent}");
            }

            var createdExercise = await response.Content.ReadFromJsonAsync<GetExerciseTemplate>();
            if (createdExercise is not null)
            {
                return createdExercise;
            }

            throw new InvalidOperationException("Failed to deserialize the response content.");

        }
        catch (HttpRequestException ex)
        {
            Console.WriteLine($"An HTTP error occurred: {ex.Message}");
            throw;
        }
        catch (Exception ex)
        {
            Console.WriteLine($"An unexpected error occurred: {ex.Message}");
            throw;
        }
    }

    public async Task<List<GetExerciseTemplate>> GetExerciseTemplatesAsync()
    {
        try
        {
            var response = await _httpClient.GetAsync("api/admin/field-template/level-template/exercise-template");
            if (!response.IsSuccessStatusCode)
            {
                if (response.StatusCode == HttpStatusCode.NotFound)
                    return [];

                var errorContent = await response.Content.ReadAsStringAsync();
                throw new HttpRequestException($"Request failed with status code {response.StatusCode}: {errorContent}");

            }

            var exercises = await response.Content.ReadFromJsonAsync<List<GetExerciseTemplate>>();
            if (exercises is not null)
            {
                return exercises;
            }

            throw new InvalidOperationException("Failed to deserialize the response content.");
        }
        catch (HttpRequestException ex)
        {
            Console.WriteLine($"An HTTP error occurred: {ex.Message}");
            throw;
        }
        catch (Exception ex)
        {
            Console.WriteLine($"An unexpected error occurred: {ex.Message}");
            throw;
        }
    }

    public async Task<GetExerciseTemplate> UpdateExerciseTemplateAsync(GetExerciseTemplate exercise)
    {
        if (exercise == null)
            throw new ArgumentNullException(nameof(exercise), "Exercise cannot be null.");

        if (exercise.Level == null)
            throw new ArgumentNullException(nameof(exercise.Level), "Exercise.Level cannot be null.");

        try
        {
            var content = new MultipartFormDataContent
            {
                // Exercise template data
                { new StringContent(exercise.Id.ToString()), "Id" },
                { new StringContent(exercise.IsTemplate.ToString()), "IsTemplate" },
                { new StringContent(exercise.Number.ToString()), "Number" },
                { new StringContent(exercise.Name), "Name" },
                { new StringContent(exercise.Description), "Description" },
                { new StringContent(exercise.PositionLeft.ToString()), "PositionLeft" },
                { new StringContent(exercise.PositionRight.ToString()), "PositionRight" },
                { new StringContent(exercise.B64), "B64" },
                
                // Level template data
                { new StringContent(exercise.LevelId.ToString()), "LevelId" },
                { new StringContent(exercise.Level.Name), "Level.Name" },
                { new StringContent(exercise.Level.Color), "Level.Color" }
            };

            // Types
            int typeIndex = 0;
            foreach (var type in exercise.Types)
            {
                content.Add(new StringContent(type.Id.ToString()), $"Types[{typeIndex}].Id");
                content.Add(new StringContent(type.IsTemplate.ToString()), $"Types[{typeIndex}].IsTemplate");
                content.Add(new StringContent(type.Name), $"Types[{typeIndex}].Name");
                typeIndex++;
            }

            // Tempos
            int tempoIndex = 0;
            foreach (var tempo in exercise.Tempos)
            {
                content.Add(new StringContent(tempo.Id.ToString()), $"Tempos[{tempoIndex}].Id");
                content.Add(new StringContent(tempo.IsTemplate.ToString()), $"Tempos[{tempoIndex}].IsTemplate");
                content.Add(new StringContent(tempo.Name), $"Tempos[{tempoIndex}].Name");
                tempoIndex++;
            }

            // Obstacles
            int obstacleIndex = 0;
            foreach (var obstacle in exercise.Obstacles)
            {
                content.Add(new StringContent(obstacle.Id.ToString()), $"Obstacles[{obstacleIndex}].Id");
                content.Add(new StringContent(obstacle.IsTemplate.ToString()), $"Obstacles[{obstacleIndex}].IsTemplate");
                content.Add(new StringContent(obstacle.Name), $"Obstacles[{obstacleIndex}].Name");
                
                if (exercise.ImageFile is not null)
                {
                    var b64 = await _imageProcessingService.ConvertToBase64Async(exercise.ImageFile);

                    content.Add(new StringContent(b64), $"Obstacles[{obstacleIndex}].B64");
                }
                else
                {
                    content.Add(new StringContent(exercise.B64), $"Obstacles[{obstacleIndex}].B64");
                }

                obstacleIndex++;
            }

            var response = await _httpClient.PutAsync($"api/admin/field-template/level-template/exercise-template/{exercise.Id}", content);

            if (!response.IsSuccessStatusCode)
            {
                var errorContent = await response.Content.ReadAsStringAsync();
                throw new HttpRequestException($"Request failed with status code {response.StatusCode}: {errorContent}");
            }

            var updatedExercise = await response.Content.ReadFromJsonAsync<GetExerciseTemplate>();
            if (updatedExercise is not null)
            {
                return updatedExercise;
            }

            throw new InvalidOperationException("Failed to deserialize the response content.");
        }
        catch (HttpRequestException ex)
        {
            Console.WriteLine($"An HTTP error occurred: {ex.Message}");
            throw;
        }
        catch (Exception ex)
        {
            Console.WriteLine($"An unexpected error occurred: {ex.Message}");
            throw;
        }
    }

    public async Task DeleteExerciseTemplateAsync(int? id)
    {
        if (id is null)
        {
            throw new ArgumentNullException(nameof(id), "ID cannot be null");
        }

        try
        {
            var response = await _httpClient.DeleteAsync($"api/admin/field-template/level-template/exercise-template/{id}");
            if (!response.IsSuccessStatusCode)
            {
                var errorContent = await response.Content.ReadAsStringAsync();
                throw new HttpRequestException($"Request failed with status code {response.StatusCode}: {errorContent}");
            }
        }
        catch (HttpRequestException ex)
        {
            Console.WriteLine($"An HTTP error occurred: {ex.Message}");
            throw;
        }
        catch (Exception ex)
        {
            Console.WriteLine($"An unexpected error occurred: {ex.Message}");
            throw;
        }
    }
}
```
