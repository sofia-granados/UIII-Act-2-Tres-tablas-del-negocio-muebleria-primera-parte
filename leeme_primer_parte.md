  
    UIII_muebleria_0387/
    ├── .venv/
    ├── backend_muebleria/
    │   ├── __init__.py
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    ├── app_muebleria/
    │   ├── __init__.py
        │   ├── admin.py
    │   ├── models.py
    │   ├── views.py
    │   ├── urls.py
    │   └── templates/
    │       ├── base.html
    │       ├── header.html
    │       ├── navbar.html
    │       ├── footer.html
    │       ├── inicio.html
    │       └── empleado/
    │           ├── agregar_empleado.html
    │           ├── ver_empleado.html
    │           ├── actualizar_empleado.html
    │           └── borrar_empleado.html
    ├── manage.py
    └── requirements.txt
# 🔧 Paso 1: Configuración Inicial (Terminal)
bash
    
    # 1. Crear carpeta del proyecto
    mkdir UIII_muebleria_0387
    cd UIII_muebleria_0387

    # 2. Abrir en VS Code
    code .

    # 3. Abrir terminal en VS Code (Ctrl + Ñ)

    # 4. Crear entorno virtual
    python -m venv .venv

    # 5. Activar entorno virtual (Windows)
    .\.venv\Scripts\activate

    # 6. Activar intérprete de Python (se activa automáticamente con el entorno virtual)

    # 7. Instalar Django
    pip install django

    # 8. Crear proyecto sin duplicar carpeta
    django-admin startproject backend_muebleria .

    # 9. Crear aplicación
    python manage.py startapp app_muebleria
  
    # 10. Crear carpetas necesarias
    mkdir app_muebleria\templates
    mkdir app_muebleria\templates\empleado

# ⚙️ Paso 2: Configuración del Proyecto
✅ backend_muebleria/settings.py
    
    python
    import os
    from pathlib import Path

    BASE_DIR = Path(__file__).resolve().parent.parent

    SECRET_KEY = 'django-insecure-muebleria-0387-clave-secreta'
    DEBUG = True
    ALLOWED_HOSTS = []

    INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_muebleria',
    ]

    MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    ]

    ROOT_URLCONF = 'backend_muebleria.urls'

    TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'app_muebleria/templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
    ]

    WSGI_APPLICATION = 'backend_muebleria.wsgi.application'

    DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
    }

    AUTH_PASSWORD_VALIDATORS = []

    LANGUAGE_CODE = 'es-mx'
    TIME_ZONE = 'America/Mexico_City'
    USE_I18N = True
    USE_TZ = True

    STATIC_URL = '/static/'
    DEFAULT_AUTO_FIELD = 'django.db.models.BigAutoField'

# ✅ backend_muebleria/urls.py
python
  
    from django.contrib import admin
    from django.urls import path, include

    urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_muebleria.urls')),
    ]

# 🗃️ Paso 3: Modelos
✅ app_muebleria/models.py

    python
    from django.db import models

    # Choices para los campos
    CARGO_CHOICES = [
    ('VENDEDOR', 'Vendedor'),
    ('GERENTE', 'Gerente'),
    ('ALMACEN', 'Almacen'),
    ('CAJERO', 'Cajero'),
    ]

    CATEGORIA_CHOICES = [
    ('SALA', 'Sala'),
    ('COMEDOR', 'Comedor'),
    ('RECAMARA', 'Recámara'),
    ('OFICINA', 'Oficina'),
    ]

    MATERIAL_CHOICES = [
    ('MADERA', 'Madera'),
    ('METAL', 'Metal'),
    ('PLASTICO', 'Plástico'),
    ('VIDRIO', 'Vidrio'),
    ]

    class Empleado(models.Model):
    fecha_contratacion = models.DateField()
    id_empleado = models.AutoField(primary_key=True)
    nombre = models.CharField(max_length=100)
    edad = models.IntegerField()
    cargo = models.CharField(max_length=20, choices=CARGO_CHOICES)
    telefono = models.CharField(max_length=15)
    sueldo = models.DecimalField(max_digits=10, decimal_places=2)
    
    def __str__(self):
        return f"{self.nombre} - {self.cargo}"

    class Sucursal(models.Model):
    id_sucursal = models.AutoField(primary_key=True)
    telefono = models.CharField(max_length=15)
    direccion = models.CharField(max_length=200)
    num_sucursal = models.IntegerField()
    encargado = models.CharField(max_length=100)
    codigo_postal = models.CharField(max_length=10)
    ciudad = models.CharField(max_length=50)
    id_empleado = models.ForeignKey(Empleado, on_delete=models.CASCADE, related_name="sucursales")
    
    def __str__(self):
        return f"Sucursal {self.num_sucursal} - {self.ciudad}"

    class Producto(models.Model):    
    nombre = models.CharField(max_length=100)
    categoria = models.CharField(max_length=20, choices=CATEGORIA_CHOICES)
    material = models.CharField(max_length=20, choices=MATERIAL_CHOICES)
    precio = models.FloatField()
    stock = models.IntegerField()
    color = models.CharField(max_length=30)
    id_producto = models.AutoField(primary_key=True)
    sucursales = models.ManyToManyField(Sucursal, related_name="productos")
    
    def __str__(self):
        return f"{self.nombre} - ${self.precio}"

# 🧠 Paso 4: Vistas
✅ app_muebleria/views.py
  
    python
    from django.shortcuts import render, redirect, get_object_or_404
    from .models import Empleado

    # Vistas para Empleados
    def inicio_empleados(request):
    empleados = Empleado.objects.all()
    return render(request, 'empleado/ver_empleado.html', {'empleados': empleados})

    def agregar_empleado(request):
    if request.method == 'POST':
        # Crear nuevo empleado con los datos del formulario
        empleado = Empleado(
            fecha_contratacion=request.POST['fecha_contratacion'],
            nombre=request.POST['nombre'],
            edad=request.POST['edad'],
            cargo=request.POST['cargo'],
            telefono=request.POST['telefono'],
            sueldo=request.POST['sueldo']
        )
        empleado.save()
        return redirect('ver_empleados')
    
    return render(request, 'empleado/agregar_empleado.html')

    def actualizar_empleados(request):
    empleados = Empleado.objects.all()
    return render(request, 'empleado/actualizar_empleado.html', {'empleados': empleados})

    def realizar_actualizacion_empleados(request, id_empleado):
    empleado = get_object_or_404(Empleado, id_empleado=id_empleado)
    
    if request.method == 'POST':
        empleado.fecha_contratacion = request.POST['fecha_contratacion']
        empleado.nombre = request.POST['nombre']
        empleado.edad = request.POST['edad']
        empleado.cargo = request.POST['cargo']
        empleado.telefono = request.POST['telefono']
        empleado.sueldo = request.POST['sueldo']
        empleado.save()
        return redirect('ver_empleados')
    
    return render(request, 'empleado/actualizar_empleado.html', {'empleado': empleado})

    def borrar_empleados(request, id_empleado):
    empleado = get_object_or_404(Empleado, id_empleado=id_empleado)
    
    if request.method == 'POST':
        empleado.delete()
        return redirect('ver_empleados')
    
    return render(request, 'empleado/borrar_empleado.html', {'empleado': empleado})

    # Vista de inicio
    def inicio(request):
    return render(request, 'inicio.html')

 # 🌐 Paso 5: URLs
✅ app_muebleria/urls.py

    python
    from django.urls import path
    from . import views

    urlpatterns = [
    path('', views.inicio, name='inicio'),
    
    # URLs para Empleados
    path('empleados/', views.inicio_empleados, name='ver_empleados'),
    path('empleados/agregar/', views.agregar_empleado, name='agregar_empleado'),
    path('empleados/actualizar/', views.actualizar_empleados, name='actualizar_empleados'),
    path('empleados/actualizar/<int:id_empleado>/', views.realizar_actualizacion_empleados, name='realizar_actualizacion'),
    path('empleados/borrar/<int:id_empleado>/', views.borrar_empleados, name='borrar_empleado'),
    ]

# 🎨 Paso 6: Plantillas HTML
✅ app_muebleria/templates/base.html

    html
    <!DOCTYPE html>
    <html lang="es">
    <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema Mueblería</title>
    
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    
    <style>
        body {
            background-color: #f8f9fa;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        .main-content {
            min-height: calc(100vh - 120px);
            padding: 20px 0;
        }
        .navbar-brand {
            font-weight: bold;
            color: #2c3e50 !important;
        }
        .card {
            border: none;
            border-radius: 15px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            margin-bottom: 20px;
        }
        .btn-primary {
            background-color: #3498db;
            border-color: #3498db;
        }
        .btn-success {
            background-color: #27ae60;
            border-color: #27ae60;
        }
        .btn-warning {
            background-color: #f39c12;
            border-color: #f39c12;
        }
        .btn-danger {
            background-color: #e74c3c;
            border-color: #e74c3c;
        }
    </style>
    </head>
    <body>
    {% include 'header.html' %}
    {% include 'navbar.html' %}
    
    <div class="container main-content">
        {% block content %}
        {% endblock %}
    </div>
    
    {% include 'footer.html' %}
    
    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    </body>
    </html>

# ✅ app_muebleria/templates/header.html

    html
    <header class="bg-light py-3 border-bottom">
    <div class="container">
        <div class="row align-items-center">
            <div class="col-md-6">
                <h1 class="h3 mb-0 text-primary">🏪 Sistema de Administración Mueblería</h1>
            </div>
            <div class="col-md-6 text-end">
                <span class="text-muted">{{ fecha_actual }}</span>
            </div>
        </div>
    </div>
    </header>

# ✅ app_muebleria/templates/navbar.html

    html
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
    <div class="container">
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav me-auto">
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'inicio' %}">
                        🏠 Inicio
                    </a>
                </li>
                
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                        👥 Empleados
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="{% url 'agregar_empleado' %}">Agregar empleados</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_empleados' %}">Ver empleados</a></li>
                        <li><a class="dropdown-item" href="{% url 'actualizar_empleados' %}">Actualizar empleados</a></li>
                    </ul>
                </li>
                
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                        🏢 Sucursal
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Agregar sucursal</a></li>
                        <li><a class="dropdown-item" href="#">Ver sucursal</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar sucursal</a></li>
                        <li><a class="dropdown-item" href="#">Borrar sucursal</a></li>
                    </ul>
                </li>
                
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                        🛋️ Productos
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Agregar productos</a></li>
                        <li><a class="dropdown-item" href="#">Ver productos</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar productos</a></li>
                        <li><a class="dropdown-item" href="#">Borrar productos</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
    </nav>

# ✅ app_muebleria/templates/footer.html

    html
    <footer class="bg-dark text-white text-center py-3 mt-5">
    <div class="container">
        <p class="mb-1">&copy; 2024 Sistema Mueblería - Todos los derechos reservados</p>
        <p class="mb-0">Creado por Perla Terrazas, CBTIS 128</p>
        <small>{{ fecha_actual }}</small>
    </div>
    </footer>

# ✅ app_muebleria/templates/inicio.html

    html
    {% extends 'base.html' %}

    {% block content %}
    <div class="row">
    <div class="col-md-8">
        <div class="card">
            <div class="card-body">
                <h2 class="card-title text-primary">Bienvenido al Sistema de Administración Mueblería</h2>
                <p class="card-text">
                    Sistema desarrollado para la gestión integral de una mueblería, incluyendo control de empleados, 
                    sucursales y productos. Desarrollado con Django y Bootstrap.
                </p>
                <h5 class="mt-4">Funcionalidades principales:</h5>
                <ul>
                    <li>Gestión de empleados</li>
                    <li>Control de sucursales</li>
                    <li>Administración de productos</li>
                    <li>Reportes y estadísticas</li>
                </ul>
            </div>
        </div>
    </div>
    <div class="col-md-4">
        <div class="card">
            <div class="card-body text-center">
                <img src="https://images.unsplash.com/photo-1555041469-a586c61ea9bc?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=500&q=80" 
                     alt="Mueblería" class="img-fluid rounded">
                <p class="mt-3 text-muted">Sistema desarrollado con tecnologías modernas</p>
            </div>
        </div>
    </div>
    </div>
    {% endblock %}

# 👥 Paso 7: Plantillas de Empleados
✅ app_muebleria/templates/empleado/agregar_empleado.html

    html
    {% extends 'base.html' %}

    {% block content %}
    <div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card">
            <div class="card-header bg-primary text-white">
                <h4 class="mb-0">➕ Agregar Nuevo Empleado</h4>
            </div>
            <div class="card-body">
                <form method="POST">
                    {% csrf_token %}
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Nombre:</label>
                            <input type="text" class="form-control" name="nombre" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Edad:</label>
                            <input type="number" class="form-control" name="edad" required>
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Fecha de Contratación:</label>
                            <input type="date" class="form-control" name="fecha_contratacion" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Cargo:</label>
                            <select class="form-select" name="cargo" required>
                                <option value="">Seleccionar cargo</option>
                                <option value="VENDEDOR">Vendedor</option>
                                <option value="GERENTE">Gerente</option>
                                <option value="ALMACEN">Almacen</option>
                                <option value="CAJERO">Cajero</option>
                            </select>
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Teléfono:</label>
                            <input type="text" class="form-control" name="telefono" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Sueldo:</label>
                            <input type="number" step="0.01" class="form-control" name="sueldo" required>
                        </div>
                    </div>
                    
                    <div class="d-grid gap-2 d-md-flex justify-content-md-end">
                        <button type="submit" class="btn btn-success me-md-2">Guardar Empleado</button>
                        <a href="{% url 'ver_empleados' %}" class="btn btn-secondary">Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
    </div>
    {% endblock %}

# ✅ app_muebleria/templates/empleado/ver_empleado.html
  
    html
    {% extends 'base.html' %}

    {% block content %}
    <div class="card">
    <div class="card-header bg-success text-white d-flex justify-content-between align-items-center">
        <h4 class="mb-0">👥 Lista de Empleados</h4>
        <a href="{% url 'agregar_empleado' %}" class="btn btn-light">➕ Agregar Nuevo</a>
    </div>
    <div class="card-body">
        {% if empleados %}
        <div class="table-responsive">
            <table class="table table-striped table-hover">
                <thead class="table-dark">
                    <tr>
                        <th>ID</th>
                        <th>Nombre</th>
                        <th>Edad</th>
                        <th>Cargo</th>
                        <th>Teléfono</th>
                        <th>Sueldo</th>
                        <th>Acciones</th>
                    </tr>
                </thead>
                <tbody>
                    {% for empleado in empleados %}
                    <tr>
                        <td>{{ empleado.id_empleado }}</td>
                        <td>{{ empleado.nombre }}</td>
                        <td>{{ empleado.edad }}</td>
                        <td>{{ empleado.get_cargo_display }}</td>
                        <td>{{ empleado.telefono }}</td>
                        <td>${{ empleado.sueldo }}</td>
                        <td>
                            <a href="#" class="btn btn-info btn-sm">👁️ Ver</a>
                            <a href="{% url 'realizar_actualizacion' empleado.id_empleado %}" class="btn btn-warning btn-sm">✏️ Editar</a>
                            <a href="{% url 'borrar_empleado' empleado.id_empleado %}" class="btn btn-danger btn-sm">🗑️ Borrar</a>
                        </td>
                    </tr>
                    {% endfor %}
                </tbody>
            </table>
        </div>
        {% else %}
        <div class="alert alert-info text-center">
            <h5>No hay empleados registrados</h5>
            <p>Comienza agregando el primer empleado al sistema.</p>
            <a href="{% url 'agregar_empleado' %}" class="btn btn-primary">Agregar Primer Empleado</a>
        </div>
        {% endif %}
    </div>
    </div>
    {% endblock %}

# ✅ app_muebleria/templates/empleado/actualizar_empleado.html

    html
    {% extends 'base.html' %}

    {% block content %}
    <div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card">
            <div class="card-header bg-warning text-white">
                <h4 class="mb-0">✏️ Actualizar Empleado</h4>
            </div>
            <div class="card-body">
                {% if empleado %}
                <form method="POST">
                    {% csrf_token %}
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Nombre:</label>
                            <input type="text" class="form-control" name="nombre" value="{{ empleado.nombre }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Edad:</label>
                            <input type="number" class="form-control" name="edad" value="{{ empleado.edad }}" required>
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Fecha de Contratación:</label>
                            <input type="date" class="form-control" name="fecha_contratacion" value="{{ empleado.fecha_contratacion|date:'Y-m-d' }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Cargo:</label>
                            <select class="form-select" name="cargo" required>
                                <option value="VENDEDOR" {% if empleado.cargo == 'VENDEDOR' %}selected{% endif %}>Vendedor</option>
                                <option value="GERENTE" {% if empleado.cargo == 'GERENTE' %}selected{% endif %}>Gerente</option>
                                <option value="ALMACEN" {% if empleado.cargo == 'ALMACEN' %}selected{% endif %}>Almacen</option>
                                <option value="CAJERO" {% if empleado.cargo == 'CAJERO' %}selected{% endif %}>Cajero</option>
                            </select>
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Teléfono:</label>
                            <input type="text" class="form-control" name="telefono" value="{{ empleado.telefono }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Sueldo:</label>
                            <input type="number" step="0.01" class="form-control" name="sueldo" value="{{ empleado.sueldo }}" required>
                        </div>
                    </div>
                    
                    <div class="d-grid gap-2 d-md-flex justify-content-md-end">
                        <button type="submit" class="btn btn-success me-md-2">Actualizar Empleado</button>
                        <a href="{% url 'ver_empleados' %}" class="btn btn-secondary">Cancelar</a>
                    </div>
                </form>
                {% else %}
                <div class="alert alert-info">
                    <h5>Selecciona un empleado para actualizar</h5>
                    <p>Ve a la lista de empleados y haz clic en "Editar" para actualizar la información.</p>
                    <a href="{% url 'ver_empleados' %}" class="btn btn-primary">Ver Lista de Empleados</a>
                </div>
                {% endif %}
            </div>
        </div>
    </div>
    </div>
    {% endblock %}

# ✅ app_muebleria/templates/empleado/borrar_empleado.html

    html
    {% extends 'base.html' %}

    {% block content %}
    <div class="row justify-content-center">
    <div class="col-md-6">
        <div class="card">
            <div class="card-header bg-danger text-white">
                <h4 class="mb-0">🗑️ Confirmar Eliminación</h4>
            </div>
            <div class="card-body text-center">
                <div class="alert alert-warning">
                    <h5>¿Estás seguro de que quieres eliminar a este empleado?</h5>
                    <p class="mb-2"><strong>Nombre:</strong> {{ empleado.nombre }}</p>
                    <p class="mb-2"><strong>Cargo:</strong> {{ empleado.get_cargo_display }}</p>
                    <p class="mb-3"><strong>ID:</strong> {{ empleado.id_empleado }}</p>
                    <p class="text-danger"><strong>⚠️ Esta acción no se puede deshacer</strong></p>
                </div>
                
                <form method="POST">
                    {% csrf_token %}
                    <div class="d-grid gap-2 d-md-flex justify-content-md-center">
                        <button type="submit" class="btn btn-danger me-md-2">Sí, Eliminar</button>
                        <a href="{% url 'ver_empleados' %}" class="btn btn-secondary">Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
    </div>
    {% endblock %}

# ⚙️ Paso 8: Configuración Admin
✅ app_muebleria/admin.py

    python
    from django.contrib import admin
    from .models import Empleado, Sucursal, Producto

    @admin.register(Empleado)
    class EmpleadoAdmin(admin.ModelAdmin):
    list_display = ('id_empleado', 'nombre', 'cargo', 'sueldo', 'fecha_contratacion')
    list_filter = ('cargo',)
    search_fields = ('nombre',)

    @admin.register(Sucursal)
    class SucursalAdmin(admin.ModelAdmin):
    list_display = ('id_sucursal', 'num_sucursal', 'ciudad', 'encargado')
    list_filter = ('ciudad',)
    search_fields = ('ciudad', 'encargado')

    @admin.register(Producto)
    class ProductoAdmin(admin.ModelAdmin):
    list_display = ('id_producto', 'nombre', 'categoria', 'precio', 'stock')
    list_filter = ('categoria', 'material')
    search_fields = ('nombre',)

# 🚀 Paso 9: Ejecutar el Proyecto
bash
   
    # 1. Activar entorno virtual
    .\.venv\Scripts\activate

    # 2. Realizar migraciones
    python manage.py makemigrations
    python manage.py migrate

    # 3. Crear superusuario (opcional)
    python manage.py createsuperuser

    # 4. Ejecutar servidor en puerto 8087
    python manage.py runserver 8087

# 🌐 Acceso a la Aplicación

    Sitio web: http://127.0.0.1:8087/

    Panel de admin: http://127.0.0.1:8087/admin/

    ✅ Funcionalidades Completas Implementadas
    ✅ Estructura completa de carpetas y archivos

    ✅ Modelos: Empleado, Sucursal, Producto

    ✅ CRUD completo para Empleados

    ✅ Diseño con Bootstrap y colores suaves

    ✅ Navegación con menús desplegables

    ✅ Plantillas organizadas y modernas

    ✅ Sin uso de forms.py

    ✅ Proyecto totalmente funcional
