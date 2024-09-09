# Biblioteca-digital
class Libro:
    def __init__(self, titulo, autor, categoria, isbn):
        self.titulo = titulo
        self.autor = autor
        self.categoria = categoria
        self.isbn = isbn

    def __repr__(self):
        return f"'{self.titulo}' por {self.autor} (ISBN: {self.isbn}) - Categoría: {self.categoria}"


class Usuario:
    def __init__(self, nombre, user_id):
        self.nombre = nombre
        self.user_id = user_id
        self.libros_prestados = []

    def prestar_libro(self, libro):
        self.libros_prestados.append(libro)

    def devolver_libro(self, libro):
        if libro in self.libros_prestados:
            self.libros_prestados.remove(libro)

    def __repr__(self):
        libros = ', '.join([libro.titulo for libro in self.libros_prestados])
        return f"Usuario: {self.nombre} (ID: {self.user_id}) - Libros Prestados: [{libros}]"


class Biblioteca:
    def __init__(self):
        self.libros = {}
        self.usuarios = set()

    def añadir_libro(self, libro):
        if libro.isbn in self.libros:
            print(f"El libro con ISBN {libro.isbn} ya está en la biblioteca.")
        else:
            self.libros[libro.isbn] = libro

    def quitar_libro(self, isbn):
        if isbn in self.libros:
            del self.libros[isbn]
        else:
            print(f"El libro con ISBN {isbn} no se encuentra en la biblioteca.")

    def registrar_usuario(self, usuario):
        if usuario.user_id in {u.user_id for u in self.usuarios}:
            print(f"El usuario con ID {usuario.user_id} ya está registrado.")
        else:
            self.usuarios.add(usuario)

    def dar_baja_usuario(self, user_id):
        self.usuarios = {u for u in self.usuarios if u.user_id != user_id}

    def prestar_libro(self, isbn, user_id):
        if isbn not in self.libros:
            print(f"No se encontró el libro con ISBN {isbn}.")
            return
        libro = self.libros[isbn]
        usuario = next((u for u in self.usuarios if u.user_id == user_id), None)
        if usuario is None:
            print(f"No se encontró el usuario con ID {user_id}.")
            return
        usuario.prestar_libro(libro)
        del self.libros[isbn]

    def devolver_libro(self, isbn, user_id):
        usuario = next((u for u in self.usuarios if u.user_id == user_id), None)
        if usuario is None:
            print(f"No se encontró el usuario con ID {user_id}.")
            return
        libro = next((libro for libro in usuario.libros_prestados if libro.isbn == isbn), None)
        if libro is None:
            print(f"El libro con ISBN {isbn} no está prestado a este usuario.")
            return
        usuario.devolver_libro(libro)
        self.libros[isbn] = libro

    def buscar_libro(self, criterio):
        resultados = [libro for libro in self.libros.values() if (criterio.lower() in libro.titulo.lower() or 
                                                                   criterio.lower() in libro.autor.lower() or
                                                                   criterio.lower() in libro.categoria.lower())]
        return resultados

    def listar_libros_prestados(self, user_id):
        usuario = next((u for u in self.usuarios if u.user_id == user_id), None)
        if usuario:
            return usuario.libros_prestados
        else:
            print(f"No se encontró el usuario con ID {user_id}.")
            return []

# Ejemplo de uso
if __name__ == "__main__":
    # Crear biblioteca
    biblioteca = Biblioteca()
    
    # Crear libros
    libro1 = Libro("Cien años de soledad", "Gabriel García Márquez", "Novela", "1234567890")
    libro2 = Libro("Don Quijote de la Mancha", "Miguel de Cervantes", "Clásico", "0987654321")
    
    # Añadir libros
    biblioteca.añadir_libro(libro1)
    biblioteca.añadir_libro(libro2)
    
    # Crear usuarios
    usuario1 = Usuario("Juan Pérez", "001")
    usuario2 = Usuario("Ana Gómez", "002")
    
    # Registrar usuarios
    biblioteca.registrar_usuario(usuario1)
    biblioteca.registrar_usuario(usuario2)
    
    # Prestar libro
    biblioteca.prestar_libro("1234567890", "001")
    
    # Listar libros prestados
    print(biblioteca.listar_libros_prestados("001"))
    
    # Devolver libro
    biblioteca.devolver_libro("1234567890", "001")
    
    # Buscar libro
    print(biblioteca.buscar_libro("Don Quijote"))

    # Quitar libro
    biblioteca.quitar_libro("0987654321")

    # Dar baja usuario
    biblioteca.dar_baja_usuario("002")
