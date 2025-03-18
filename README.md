# tarea7

#MIGRACION
public function up()
{
    Schema::create('asignaturas', function (Blueprint $table) {
        $table->id();
        $table->string('nombre');
        $table->timestamps();
    });

    Schema::create('actividades', function (Blueprint $table) {
        $table->id();
        $table->unsignedBigInteger('asignatura_id');
        $table->string('tipo');
        $table->decimal('calificacion', 5, 2);
        $table->date('fecha');
        $table->text('comentario')->nullable();
        $table->timestamps();

        $table->foreign('asignatura_id')->references('id')->on('asignaturas')->onDelete('cascade');
    });
}

#Configuracion de modelo
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Asignatura extends Model
{
    use HasFactory;

    protected $fillable = ['nombre'];

    public function actividades()
    {
        return $this->hasMany(Actividad::class);
    }
}
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Actividad extends Model
{
    use HasFactory;

    protected $fillable = ['asignatura_id', 'tipo', 'calificacion', 'fecha', 'comentario'];

    public function asignatura()
    {
        return $this->belongsTo(Asignatura::class);
    }
}

#configuracion del controlador
namespace App\Http\Controllers;

use App\Models\Asignatura;
use App\Models\Actividad;
use Illuminate\Http\Request;

class AsignaturaController extends Controller
{
    public function index()
    {
        $asignaturas = Asignatura::all();
        return view('asignaturas.index', compact('asignaturas'));
    }

    public function show(Asignatura $asignatura)
    {
        return view('asignaturas.show', compact('asignatura'));
    }

    public function create()
    {
        return view('asignaturas.create');
    }

    public function store(Request $request)
    {
        Asignatura::create($request->all());
        return redirect()->route('asignaturas.index');
    }

    public function edit(Asignatura $asignatura)
    {
        return view('asignaturas.edit', compact('asignatura'));
    }

    public function update(Request $request, Asignatura $asignatura)
    {
        $asignatura->update($request->all());
        return redirect()->route('asignaturas.index');
    }

    public function destroy(Asignatura $asignatura)
    {
        $asignatura->delete();
        return redirect()->route('asignaturas.index');
    }
}
#configuracion de rutas
use App\Http\Controllers\AsignaturaController;

Route::resource('asignaturas', AsignaturaController::class);
