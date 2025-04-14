# Proyecto-final-sistema-academico
using System;
using System.Collections.Generic;
using System.Linq;

namespace SistemaAcademico
{
    abstract class Persona
    {
        public string Nombre { get; private set; }
        public int Edad { get; private set; }

        public Persona(string nombre, int edad)
        {
            Nombre = nombre;
            Edad = edad;
        }

        public abstract void MostrarInfo();
    }

    class Estudiante : Persona
    {
        public int Matricula { get; private set; }
        public List<Curso> CursosInscritos { get; private set; }

        public Estudiante(string nombre, int edad, int matricula)
            : base(nombre, edad)
        {
            Matricula = matricula;
            CursosInscritos = new List<Curso>();
        }

        public void InscribirseEnCurso(Curso curso)
        {
            if (!CursosInscritos.Contains(curso))
            {
                CursosInscritos.Add(curso);
                curso.AgregarEstudiante(this);
            }
        }

        public override void MostrarInfo()
        {
            Console.WriteLine($"🎓 Estudiante: {Nombre}, Edad: {Edad}, Matrícula: {Matricula}");
            if (CursosInscritos.Count == 0)
            {
                Console.WriteLine("  Sin cursos inscritos.");
            }
            else
            {
                Console.WriteLine("  Cursos inscritos:");
                foreach (var curso in CursosInscritos)
                {
                    Console.WriteLine($"    - {curso.Nombre}");
                }
            }
        }
    }

    class Curso
    {
        public string Codigo { get; private set; }
        public string Nombre { get; private set; }
        public List<Estudiante> Estudiantes { get; private set; }
        private Dictionary<int, double> Calificaciones;

        public Curso(string codigo, string nombre)
        {
            Codigo = codigo;
            Nombre = nombre;
            Estudiantes = new List<Estudiante>();
            Calificaciones = new Dictionary<int, double>();
        }

        public void AgregarEstudiante(Estudiante estudiante)
        {
            if (!Estudiantes.Contains(estudiante))
            {
                Estudiantes.Add(estudiante);
            }
        }

        public void AsignarCalificacion(int matricula, double calificacion)
        {
            Calificaciones[matricula] = calificacion;
        }

        public double CalcularPromedio()
        {
            return Calificaciones.Count == 0 ? 0 : Calificaciones.Values.Average();
        }

        public void MostrarInfo()
        {
            Console.WriteLine($"📘 Curso: {Nombre} ({Codigo})");
            if (Estudiantes.Count == 0)
            {
                Console.WriteLine("  Sin estudiantes inscritos.");
            }
            else
            {
                Console.WriteLine("  Estudiantes inscritos:");
                foreach (var e in Estudiantes)
                {
                    string nota = Calificaciones.ContainsKey(e.Matricula) ? $" - Nota: {Calificaciones[e.Matricula]}" : "";
                    Console.WriteLine($"    - {e.Nombre} (Matrícula: {e.Matricula}){nota}");
                }
                Console.WriteLine($"  Promedio del curso: {CalcularPromedio():0.00}");
            }
        }
    }

    class SistemaAcademico
    {
        public List<Estudiante> Estudiantes { get; private set; }
        public List<Curso> Cursos { get; private set; }

        public SistemaAcademico()
        {
            Estudiantes = new List<Estudiante>();
            Cursos = new List<Curso>();
        }

        public void MostrarMenu()
        {
            while (true)
            {
                Console.WriteLine("\n===== SISTEMA ACADÉMICO =====");
                Console.WriteLine("1. Registrar Estudiante");
                Console.WriteLine("2. Registrar Curso");
                Console.WriteLine("3. Inscribir Estudiante en Curso");
                Console.WriteLine("4. Asignar Calificación");
                Console.WriteLine("5. Mostrar Estudiantes");
                Console.WriteLine("6. Mostrar Cursos");
                Console.WriteLine("0. Salir");
                Console.Write("Seleccione una opción: ");
                string opcion = Console.ReadLine();

                switch (opcion)
                {
                    case "1":
                        RegistrarEstudiante();
                        break;
                    case "2":
                        RegistrarCurso();
                        break;
                    case "3":
                        InscribirEstudiante();
                        break;
                    case "4":
                        AsignarNota();
                        break;
                    case "5":
                        MostrarEstudiantes();
                        break;
                    case "6":
                        MostrarCursos();
                        break;
                    case "0":
                        return;
                    default:
                        Console.WriteLine("Opción inválida.");
                        break;
                }
            }
        }

        private void RegistrarEstudiante()
        {
            Console.Write("Nombre: ");
            string nombre = Console.ReadLine();
            Console.Write("Edad: ");
            int edad = int.Parse(Console.ReadLine());
            Console.Write("Matrícula: ");
            r int matricula = int.Parse(Console.ReadLine());

            Estudiantes.Add(new Estudiante(nombre, edad, matricula));
            Console.WriteLine("✅ Estudiante registrado.");
        }

        private void RegistrarCurso()
        {
            Console.Write("Código del curso: ");
            string codigo = Console.ReadLine();
            Console.Write("Nombre del curso: ");
            string nombre = Console.ReadLine();

            Cursos.Add(new Curso(codigo, nombre));
            Console.WriteLine("✅ Curso registrado.");
        }

        private void InscribirEstudiante()
        {
            Estudiante est = BuscarEstudiante();
            Curso curso = BuscarCurso();

            if (est != null && curso != null)
            {
                est.InscribirseEnCurso(curso);
                Console.WriteLine("✅ Estudiante inscrito en el curso.");
            }
        }

        private void AsignarNota()
        {
            Curso curso = BuscarCurso();
            if (curso == null) return;

            Console.Write("Matrícula del estudiante: ");
            int matricula = int.Parse(Console.ReadLine());
            Console.Write("Nota: ");
            double nota = double.Parse(Console.ReadLine());

            curso.AsignarCalificacion(matricula, nota);
            Console.WriteLine("✅ Calificación asignada.");
        }

        private Estudiante BuscarEstudiante()
        {
            Console.Write("Ingrese matrícula: ");
            int matricula = int.Parse(Console.ReadLine());
            return Estudiantes.FirstOrDefault(e => e.Matricula == matricula);
        }

        private Curso BuscarCurso()
        {
            Console.Write("Ingrese código del curso: ");
            string codigo = Console.ReadLine();
            return Cursos.FirstOrDefault(c => c.Codigo == codigo);
        }

        public void MostrarEstudiantes()
        {
            if (Estudiantes.Count == 0)
            {
                Console.WriteLine("❗ No hay estudiantes registrados.");
                return;
            }

            foreach (var estudiante in Estudiantes)
            {
                estudiante.MostrarInfo();
                Console.WriteLine();
            }
        }

        public void MostrarCursos()
        {
            if (Cursos.Count == 0)
            {
                Console.WriteLine("❗ No hay cursos registrados.");
                return;
            }

            foreach (var curso in Cursos)
            {
                curso.MostrarInfo();
                Console.WriteLine();
            }
        }
    }

    class Program
    {
        static void Main()
        {
            SistemaAcademico sistema = new SistemaAcademico();
            sistema.MostrarMenu();
        }
    }
}
