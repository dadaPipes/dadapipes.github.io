# Server

### Controller

```CreateExerciseTemplate``` og ```UpdateExerciseTemplate``` er begge de samme objekter som er brugt i Blazor komponenterne.
Det vil sige at de har den samme validering. Hvis der skulle være en ondsindet bruger der forsøger sig med at sende korupt data ned til serveren, Fx 150% rabat, så får brugeren ikke penge tilbage, hver gang at de køber noget.
Nu er dette ikke en online shop, men ideen er den samme. Vi vil kun have saniteret data ned til vores server, hvor de basekyttede dele af forretings logikken ligger.

Det er ikke altid at det er nok at sanitere data ved kun at lade bestemte typer af data med bestemte navne få adgang.
Filer kan gå hen og være et et våben, som [kryptonit](https://frpnet.net/wp-content/uploads/2016/02/Kryptonite-Batman-V-Superman-1024x569.jpg) er for Superman. Man ved aldrig hvad der gemmer sig, uanset hvad file typen er, da det kan [spoofes](https://cloudprotection.withsecure.com/blog/2024/05/30/countering-the-risks-of-file-type-spoofing-in-cybersecurity/#:~:text=Attackers%20often%20employ%20a%20simple%20trick%20called%20file,like%20a%20text%20%28.txt%29%20or%20image%20file%20%28.jpg%29.). Det har ikke været et særligt højt krav, og har derfor ikke haft et så stort fokus, da det er meget få og betroede brugere der kommer til at have adgang til at uploade filer. Derfor er det ikke noget der er blevet implementeret.

Man kan bruge data annotations til at få ASP.NET Core til at lave json output som kan bruges af biblioteker som Swagger til at lave testbar dokumentation.
Eksemplet herunder vil dokumentere at enpointed kan give en Status code 400 BadRequest.  
```[ProducesResponseType(StatusCodes.Status400BadRequest)]```

Hvad jeg så fandt ud af senere hen var at det var fuldstændigt unødigt, da de kan dokumentere sig selv med [web API conventions](https://learn.microsoft.com/en-us/aspnet/core/web-api/advanced/conventions?view=aspnetcore-8.0).
Så sparer man da de linier kode.

```csharp
namespace DogObedience.Server.Features.Admin.Templates.ExerciseTemplate;

[ApiExplorerSettings(GroupName = "admin/templates")]
[Authorize(Roles = "Admin")]
[Route("api/admin/field-template/level-template/exercise-template")]
[ApiController]

public class ExerciseController : ControllerBase
{
    private readonly ExerciseService _exerciseService;

    public ExerciseController(ExerciseService service)
    {
        _exerciseService = service;
    }

    [HttpPost]
    [ProducesResponseType(StatusCodes.Status201Created, Type = typeof(GetExerciseTemplate))]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    public async Task<IActionResult> CreateExerciseTemplate([FromForm] CreateExerciseTemplate exercise)
    {
        var result = await _exerciseService.CreateExerciseTemplateAsync(exercise);

        return StatusCode(201, result.Value);
    }

    [HttpGet]
    [ProducesResponseType(StatusCodes.Status200OK, Type = typeof(List<GetExerciseTemplate>))]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    public async Task<IActionResult> GetAllExerciseTemplates()
    {
        var result = await _exerciseService.GetAllExerciseTemplatesAsync();

        if (result.IsFailed)
            return NotFound(result.Errors);

        return Ok(result.Value);
    }

    [HttpPut("{id}")]
    public async Task<IActionResult> UpdateExerciseTemplate(int id, [FromForm] UpdateExerciseTemplate exercise)
    {
        var updatedExercise = await _exerciseService.UpdateExerciseTemplate(id, exercise);

        return Ok(updatedExercise);
    }

    [HttpDelete("{id}")]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public async Task<IActionResult> DeleteExerciseTemplate(int id)
    {
        var result = await _exerciseService.DeleteExerciseTemplate(id);

        if (result.IsFailed)
            return NotFound(result.Errors);

        return NoContent();
    }
}
```

### Service

```csharp
public async Task<GetExerciseTemplate> UpdateExerciseTemplate(int dtoId, UpdateExerciseTemplate exerciseToUpdate)
    {
        var existingExercise = await _context.Exercises
                                             .Include(e => e.Tempos)
                                             .Include(e => e.Level)
                                             .Include(e => e.Types)
                                             .Include(e => e.Obstacles)
                                             .FirstOrDefaultAsync(e => e.Id == dtoId)
                                             ?? throw new Exception("Exercise template not found.");

        // Update basic properties of the exercise
        _context.Entry(existingExercise).CurrentValues.SetValues(exerciseToUpdate);

        // Update the LevelId
        if (exerciseToUpdate.LevelId != 0)
        {
            var existingLevel = await _context.Levels.FirstOrDefaultAsync(l => l.Id == exerciseToUpdate.LevelId)
                                 ?? throw new Exception("Level not found.");

            existingExercise.LevelId = existingLevel.Id;
        }

        // Update Types
        var typesToRemove = existingExercise.Types
                             .Where(existingType => exerciseToUpdate.Types.All(t => t.Id != existingType.Id))
                             .ToList();

        _context.ExerciseTypes.RemoveRange(typesToRemove);

        foreach (var type in exerciseToUpdate.Types)
        {
            var existingType = existingExercise.Types.FirstOrDefault(et => et.Id == type.Id);
            if (existingType != null)
            {
                _context.Entry(existingType).CurrentValues.SetValues(type);
            }
            else
            {
                existingExercise.Types.Add(new ExerciseType
                {
                    Name = type.Name,
                    IsTemplate = type.IsTemplate,
                    ExerciseId = dtoId // Set foreign key
                });
            }
        }

        // Update Tempos
        var temposToRemove = existingExercise.Tempos
                             .Where(existingTempo => exerciseToUpdate.Tempos.All(t => t.Id != existingTempo.Id))
                             .ToList();

        _context.ExerciseTempos.RemoveRange(temposToRemove);

        foreach (var tempo in exerciseToUpdate.Tempos)
        {
            var existingTempo = existingExercise.Tempos.FirstOrDefault(et => et.Id == tempo.Id);
            if (existingTempo != null)
            {
                _context.Entry(existingTempo).CurrentValues.SetValues(tempo);
            }
            else
            {
                existingExercise.Tempos.Add(new ExerciseTempo
                {
                    Name = tempo.Name,
                    IsTemplate = tempo.IsTemplate,
                    ExerciseId = dtoId // Set foreign key
                });
            }
        }

        // Update Obstacles
        var obstaclesToRemove = existingExercise.Obstacles
                                 .Where(existingObstacle => exerciseToUpdate.Obstacles.All(o => o.Id != existingObstacle.Id))
                                 .ToList();

        _context.Obstacles.RemoveRange(obstaclesToRemove);

        foreach (var obstacle in exerciseToUpdate.Obstacles)
        {
            var existingObstacle = existingExercise.Obstacles.FirstOrDefault(o => o.Id == obstacle.Id);
            if (existingObstacle != null)
            {
                _context.Entry(existingObstacle).CurrentValues.SetValues(obstacle);
            }
            else
            {
                existingExercise.Obstacles.Add(new Obstacle
                {
                    Name = obstacle.Name,
                    IsTemplate = obstacle.IsTemplate,
                    B64 = obstacle.B64,
                    ExerciseId = dtoId // Set foreign key
                });
            }
        }

        // Save changes to the database
        await _context.SaveChangesAsync();

        // Map the updated exercise to the DTO and return
        var updatedExercise = _map.ToGetExercise(existingExercise);

        return updatedExercise;
    }

    async Task<string> ConvertToB64(IFormFile image)
    {
        await using var ms = new MemoryStream();
        await image.CopyToAsync(ms);
        var bytes = ms.ToArray();
        var b64 = Convert.ToBase64String(bytes);

        return $"data:{image.ContentType};base64,{b64}";
    }
```

#### Mapper

```csharp
public void UpdateToEntity(Exercise e, UpdateExerciseTemplate dto)
    {
        // Update basic properties
        e.Id            = dto.Id;
        e.Number        = dto.Number;
        e.IsTemplate    = dto.IsTemplate;
        e.Name          = dto.Name;
        e.Description   = dto.Description;
        e.PositionLeft  = dto.PositionLeft;
        e.PositionRight = dto.PositionRight;
        e.B64           = dto.B64;
        e.LevelId       = dto.LevelId;
        // Remove types that exist in the database but not in the DTO
        var typesToRemove = e.Types
            .Where(existingType => !dto.Types.Any(dtoType => dtoType.Id == existingType.Id))
            .ToList();

        foreach (var typeToRemove in typesToRemove)
        {
            e.Types.Remove(typeToRemove);
        }

        // Add or update types from DTO
        foreach (var type in dto.Types)
        {
            var existingType = e.Types.FirstOrDefault(et => et.Id == type.Id);
            if (existingType != null)
            {
                // Update existing entity
                existingType.IsTemplate = false;
                existingType.Name = type.Name;
            }
            else
            {
                // Add new entity
                e.Types.Add(new ExerciseType
                {
                    IsTemplate = false,
                    Name = type.Name
                });
            }
        }

        // Update Tempos
        // Find existing tempos to remove
        var temposToRemove = e.Tempos
            .Where(existingTempo => dto.Tempos.All(dtoTempo => dtoTempo.Id != existingTempo.Id))
            .ToList();

        foreach (var tempoToRemove in temposToRemove)
        {
            e.Tempos.Remove(tempoToRemove);
        }

        // Add or update tempos from DTO
        foreach (var tempoDto in dto.Tempos)
        {
            var existingTempo = e.Tempos.FirstOrDefault(et => et.Id == tempoDto.Id);
            if (existingTempo != null)
            {
                // Update existing entity
                existingTempo.Name = tempoDto.Name;
            }
            else
            {
                // Add new entity
                e.Tempos.Add(new ExerciseTempo
                {
                    IsTemplate = false,
                    Name = tempoDto.Name
                    
                });
            }
        }
    }
```

De metoder der er gennemgået her er nok til den slemme side når det kommer til kompleksitet.
Men det illusterer meget godt hvordan mindre at målet med færre driftomkostninger kan få implementerings- og vedligeldelsesomkostningerne til at stige.
