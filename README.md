using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using TuProyectoBiblioteca.Models; // Asegúrate de usar el namespace correcto

namespace TuProyectoBiblioteca.Controllers // Ajusta el namespace
{
    [Route("api/[controller]")]
    [ApiController]
    public class AutoresController : ControllerBase
    {
        private readonly AppDbContext _context; // Tu DbContext (Entity Framework Core)

        public AutoresController(AppDbContext context)
        {
            _context = context;
        }

        // GET: api/Autores
        [HttpGet]
        public async Task<ActionResult<IEnumerable<Autor>>> GetAutores()
        {
            return await _context.Autores.ToListAsync();
        }

        // GET: api/Autores/5
        [HttpGet("{id}")]
        public async Task<ActionResult<Autor>> GetAutor(int id)
        {
            var autor = await _context.Autores.FindAsync(id);

            if (autor == null)
            {
                return NotFound();
            }

            return autor;
        }

        // POST: api/Autores
        [HttpPost]
        public async Task<ActionResult<Autor>> PostAutor(Autor autor)
        {
            _context.Autores.Add(autor);
            await _context.SaveChangesAsync();

            return CreatedAtAction(nameof(GetAutor), new { id = autor.AutorId }, autor);
        }

        // PUT: api/Autores/5
        [HttpPut("{id}")]
        public async Task<IActionResult> PutAutor(int id, Autor autor)
        {
            if (id != autor.AutorId)
            {
                return BadRequest();
            }

            _context.Entry(autor).State = EntityState.Modified;

            try
            {
                await _context.SaveChangesAsync();
            }
            catch (DbUpdateConcurrencyException)
            {
                if (!AutorExists(id))
                {
                    return NotFound();
                }
                else
                {
                    throw;
                }
            }

            return NoContent();
        }

        // DELETE: api/Autores/5
        [HttpDelete("{id}")]
        public async Task<IActionResult> DeleteAutor(int id)
        {
            var autor = await _context.Autores.FindAsync(id);
            if (autor == null)
            {
                return NotFound();
            }

            _context.Autores.Remove(autor);
            await _context.SaveChangesAsync();

            return NoContent();
        }

        private bool AutorExists(int id)
        {
            return _context.Autores.Any(e => e.AutorId == id);
        }
    }
}


using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;

namespace TuProyectoBiblioteca.Models  // Ajusta el namespace a tu proyecto
{
    public class Categoria
    {
        [Key]
        public int CategoriaId { get; set; }

        [Required(ErrorMessage = "El Nombre de la Categoría es requerido.")]
        [MaxLength(100, ErrorMessage = "El Nombre no puede exceder los 100 caracteres.")]
        public string Nombre { get; set; }

        [MaxLength(200, ErrorMessage = "La Descripción no puede exceder los 200 caracteres.")]
        public string Descripcion { get; set; }

        public ICollection<LibroCategoria> LibrosCategorias { get; set; } // Propiedad de navegación para la relación muchos a muchos
    }

    public class LibroCategoria
    {
        public int LibroId { get; set; }
        public Libro Libro { get; set; }

        public int CategoriaId { get; set; }
        public Categoria Categoria { get; set; }
    }
}


using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace TuProyectoBiblioteca.Models  // Ajusta el namespace a tu proyecto
{
    public class DetallePrestamo
    {
        [Key]
        public int DetallePrestamoId { get; set; }

        [ForeignKey("Prestamo")]
        public int PrestamoId { get; set; }
        public Prestamo Prestamo { get; set; } // Propiedad de navegación

        [ForeignKey("Libro")]
        public int LibroId { get; set; }
        public Libro Libro { get; set; } // Propiedad de navegación

        [Required(ErrorMessage = "La Fecha de Devolución Esperada es requerida.")]
        public DateTime FechaDevolucionEsperada { get; set; }

        public DateTime? FechaDevolucionReal { get; set; }

        [Required(ErrorMessage = "El Estado del Detalle es requerido.")]
        [MaxLength(20, ErrorMessage = "El Estado del Detalle no puede exceder los 20 caracteres.")]
        public string EstadoDetalle { get; set; } // Ej: "Activo", "Devuelto", "Pendiente"

        [MaxLength(50, ErrorMessage = "La Copia del Libro no puede exceder los 50 caracteres.")]
        public string CopiaLibro { get; set; } // Identificador de la copia (ej: "A-1", "B-2")
    }
}


using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;

namespace TuProyectoBiblioteca.Models  // Ajusta el namespace a tu proyecto
{
    public class Editorial
    {
        [Key]
        public int EditorialId { get; set; }

        [Required(ErrorMessage = "El Nombre de la Editorial es requerido.")]
        [MaxLength(100, ErrorMessage = "El Nombre no puede exceder los 100 caracteres.")]
        public string Nombre { get; set; }

        [MaxLength(200, ErrorMessage = "La Dirección no puede exceder los 200 caracteres.")]
        public string Direccion { get; set; }

        [MaxLength(20, ErrorMessage = "El Teléfono no puede exceder los 20 caracteres.")]
        public string Telefono { get; set; }

        [EmailAddress(ErrorMessage = "El Correo electrónico no es válido.")]
        [MaxLength(150, ErrorMessage = "El Correo no puede exceder los 150 caracteres.")]
        public string Correo { get; set; }

        [Url(ErrorMessage = "El Sitio Web no es una URL válida.")]
        [MaxLength(150, ErrorMessage = "El Sitio Web no puede exceder los 150 caracteres.")]
        public string SitioWeb { get; set; }

        public ICollection<Libro> Libros { get; set; } // Propiedad de navegación
    }
}


using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;
using System.Text.Json.Serialization;

namespace TuProyectoBiblioteca.Models  // Ajusta el namespace a tu proyecto
{
    public class Libro
    {
        [Key]
        public int LibroId { get; set; }

        [Required(ErrorMessage = "El Título es requerido.")]
        [MaxLength(200, ErrorMessage = "El Título no puede exceder los 200 caracteres.")]
        public string Titulo { get; set; }

        [Required(ErrorMessage = "El ISBN es requerido.")]
        [MaxLength(20, ErrorMessage = "El ISBN no puede exceder los 20 caracteres.")]
        public string ISBN { get; set; }

        public DateTime? FechaPublicacion { get; set; }

        [MaxLength(50, ErrorMessage = "El Género no puede exceder los 50 caracteres.")]
        public string Genero { get; set; }

        [MaxLength(int.MaxValue, ErrorMessage = "La Sinopsis es demasiado larga.")]
        public string Sinopsis { get; set; }

        [ForeignKey("Autor")] // Especifica la relación con Autor
        public int AutorId { get; set; }

        public Autor Autor { get; set; } // Propiedad de navegación hacia Autor

    }
}


using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace TuProyectoBiblioteca.Models  // Ajusta el namespace a tu proyecto
{
    public class Prestamo
    {
        [Key]
        public int PrestamoId { get; set; }

        [Required(ErrorMessage = "La Fecha de Préstamo es requerida.")]
        public DateTime FechaPrestamo { get; set; }

        [Required(ErrorMessage = "La Fecha de Devolución Esperada es requerida.")]
        public DateTime FechaDevolucionEsperada { get; set; }

        public DateTime? FechaDevolucionReal { get; set; } // Permitir nulos (el libro puede no haberse devuelto aún)

        [Required(ErrorMessage = "El Estado del Préstamo es requerido.")]
        [MaxLength(20, ErrorMessage = "El Estado del Préstamo no puede exceder los 20 caracteres.")]
        public string EstadoPrestamo { get; set; } // Ej: "Activo", "Devuelto", "Vencido"

        [ForeignKey("Libro")]
        public int LibroId { get; set; }
        public Libro Libro { get; set; } // Propiedad de navegación

        [ForeignKey("Usuario")]
        public int UsuarioId { get; set; }
        public Usuario Usuario { get; set; } // Propiedad de navegación
    }
}


sing System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace TuProyectoBiblioteca.Models  // Ajusta el namespace a tu proyecto
{
    public class Reserva
    {
        [Key]
        public int ReservaId { get; set; }

        [Required(ErrorMessage = "La Fecha de Reserva es requerida.")]
        public DateTime FechaReserva { get; set; }

        [Required(ErrorMessage = "La Fecha Disponible Estimada es requerida.")]
        public DateTime FechaDisponibleEstimada { get; set; }

        public DateTime? FechaNotificacion { get; set; } // Puede ser nula si aún no se notifica

        [Required(ErrorMessage = "El Estado de la Reserva es requerido.")]
        [MaxLength(20, ErrorMessage = "El Estado de la Reserva no puede exceder los 20 caracteres.")]
        public string EstadoReserva { get; set; } // Ej: "Pendiente", "Activa", "Cancelada", "Completada"

        [ForeignKey("Libro")]
        public int LibroId { get; set; }
        public Libro Libro { get; set; } // Propiedad de navegación

        [ForeignKey("Usuario")]
        public int UsuarioId { get; set; }
        public Usuario Usuario { get; set; } // Propiedad de navegación
    }
}


using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;

namespace TuProyectoBiblioteca.Models  // Ajusta el namespace a tu proyecto
{
    public class Rol
    {
        [Key]
        public int RolId { get; set; }

        [Required(ErrorMessage = "El Nombre del Rol es requerido.")]
        [MaxLength(50, ErrorMessage = "El Nombre no puede exceder los 50 caracteres.")]
        public string Nombre { get; set; }

        [MaxLength(200, ErrorMessage = "La Descripción no puede exceder los 200 caracteres.")]
        public string Descripcion { get; set; }

        public ICollection<UsuarioRol> UsuariosRoles { get; set; } // Propiedad de navegación para la relación muchos a muchos
    }

    public class UsuarioRol
    {
        public int UsuarioId { get; set; }
        public Usuario Usuario { get; set; } // Asumo que tienes una clase Usuario

        public int RolId { get; set; }
        public Rol Rol { get; set; }
    }
}


using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;

namespace TuProyectoBiblioteca.Models  // Ajusta el namespace a tu proyecto
{
    public class Ubicacion
    {
        [Key]
        public int UbicacionId { get; set; }

        [Required(ErrorMessage = "La Sección es requerida.")]
        [MaxLength(50, ErrorMessage = "La Sección no puede exceder los 50 caracteres.")]
        public string Seccion { get; set; }

        [Required(ErrorMessage = "El Estante es requerido.")]
        [MaxLength(50, ErrorMessage = "El Estante no puede exceder los 50 caracteres.")]
        public string Estante { get; set; }

        [Required(ErrorMessage = "La Fila es requerida.")]
        [MaxLength(50, ErrorMessage = "La Fila no puede exceder los 50 caracteres.")]
        public string Fila { get; set; }

        [Required(ErrorMessage = "El Código de Ubicación es requerido.")]
        [MaxLength(100, ErrorMessage = "El Código de Ubicación no puede exceder los 100 caracteres.")]
        public string CodigoUbicacion { get; set; }

        public ICollection<Libro> Libros { get; set; } // Propiedad de navegación
    }
}


using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;

namespace TuProyectoBiblioteca.Models  // Ajusta el namespace a tu proyecto
{
    public class Usuario
    {
        [Key]
        public int UsuarioId { get; set; }

        [Required(ErrorMessage = "El Nombre de Usuario es requerido.")]
        [MaxLength(50, ErrorMessage = "El Nombre de Usuario no puede exceder los 50 caracteres.")]
        public string NombreUsuario { get; set; }

        [EmailAddress(ErrorMessage = "El Correo electrónico no es válido.")]
        [MaxLength(150, ErrorMessage = "El Correo electrónico no puede exceder los 150 caracteres.")]
        public string Correo { get; set; }

        [Required(ErrorMessage = "La Contraseña es requerida.")]
        [MaxLength(255, ErrorMessage = "La Contraseña no puede exceder los 255 caracteres.")]
        public string Contraseña { get; set; }  //  ¡NUNCA almacenes la contraseña sin hashear!

        [MaxLength(20, ErrorMessage = "El Teléfono no puede exceder los 20 caracteres.")]
        public string Telefono { get; set; }

        [MaxLength(200, ErrorMessage = "La Dirección no puede exceder los 200 caracteres.")]
        public string Direccion { get; set; }

        public DateTime FechaRegistro { get; set; }

        public bool Estado { get; set; } //  Ej: true = Activo, false = Inactivo

        public ICollection<Prestamo> Prestamos { get; set; } // Propiedad de navegación (opcional, para préstamos)
        public ICollection<Reserva> Reservas { get; set; }   // Propiedad de navegación (opcional, para reservas)

        public ICollection<UsuarioRol> UsuariosRoles { get; set; } // Propiedad de navegación (opcional, para roles)
    }
}
