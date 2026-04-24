¡Entendido! Vamos a construir este ecosistema de **Agente Global (.agents)** orientado a automatización y el desarrollo de la app veterinaria. Este enfoque permite que diferentes "skills" (diseño, código, scraping) operen bajo una estructura organizada.

---

## 1. Estructura del Agente Global (.agents)

Primero, definimos la estructura de carpetas para tu habilidad maestra. En el root de tu espacio de trabajo, crea lo siguiente:

```text
.agents/
├── SKILL.md                # Definición de capacidades del agente
├── scripts/                # Automatizaciones (bash, dart, python)
├── ejemplos/               # Plantillas de código y UI
└── resources/              # Activos, iconos y esquemas de datos
```

### SKILL.md (Configuración)
Este archivo instruye al agente sobre cómo debe comportarse:
> **Skill Name:** Agente Global Veterinaria
> **Capabilities:** > - `skill-diseño`: Generación de temas y UI responsiva.
> - `skill-codigo`: Implementación de CRUD y lógica de Firebase.
> - `skill-scraping`: Recolección de datos de productos veterinarios externos.

---

## 2. Preparación del Entorno (Prerrequisitos)

Antes de codificar, debemos asegurar que el "motor" esté listo. Ejecuta estos comandos en tu terminal de **VS Code** o **Antigravity**:

### Verificación de Herramientas
```bash
# Verificar Flutter
flutter --version

# Verificar Firebase CLI (Si no lo tienes: npm install -g firebase-tools)
firebase --version
```

### Configuración de Firebase
1. **Login:** `firebase login` (Sigue el enlace en el navegador).
2. **Conectividad:** `flutterfire configure`. Selecciona tu proyecto creado en Console Firebase y elige la plataforma **Android**.
3. **Firestore:** Asegúrate de que en la consola de Firebase, la base de datos Firestore esté en **Modo de Prueba** o con reglas de escritura permitidas.

---

## 3. Configuración del Proyecto: `proyectoveterinaria`

Aunque mencionaste "proyectobarberia", seguiremos la lógica de la **Veterinaria** como solicitaste al final.

### pubspec.yaml
Añade las dependencias necesarias:
```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.x.x
  cloud_firestore: ^4.x.x
  cupertino_icons: ^1.0.2
```

---

## 4. Desarrollo del CRUD de Mascotas (.dart)

Siguiendo una secuencia lógica, organizamos los archivos en `lib/`:

### A. Modelo de Datos (`lib/models/pet_model.dart`)
```dart
class Pet {
  String id;
  String nombre;
  String especie;
  int edad;

  Pet({required this.id, required this.nombre, required this.especie, required this.edad});

  Map<String, dynamic> toMap() => {
    "nombre": nombre,
    "especie": especie,
    "edad": edad,
  };

  factory Pet.fromFirestore(Map<String, dynamic> data, String id) {
    return Pet(
      id: id,
      nombre: data['nombre'] ?? '',
      especie: data['especie'] ?? '',
      edad: data['edad'] ?? 0,
    );
  }
}
```

### B. Servicio Firestore (`lib/services/firebase_service.dart`)
```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/pet_model.dart';

class FirebaseService {
  final CollectionReference petsRef = FirebaseFirestore.instance.collection('mascotas');

  Stream<List<Pet>> getPets() {
    return petsRef.snapshots().map((snapshot) =>
        snapshot.docs.map((doc) => Pet.fromFirestore(doc.data() as Map<String, dynamic>, doc.id)).toList());
  }

  Future<void> addPet(Pet pet) => petsRef.add(pet.toMap());
  Future<void> updatePet(Pet pet) => petsRef.doc(pet.id).update(pet.toMap());
  Future<void> deletePet(String id) => petsRef.doc(id).delete();
}
```

### C. UI: Pantalla Principal y CRUD (`lib/screens/home_screen.dart`)
Esta pantalla integra la navegación y el listado.

```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';
import '../models/pet_model.dart';

class HomeScreen extends StatelessWidget {
  final FirebaseService _service = FirebaseService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Veterinaria Agente")),
      body: StreamBuilder<List<Pet>>(
        stream: _service.getPets(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          var pets = snapshot.data!;
          return ListView.builder(
            itemCount: pets.length,
            itemBuilder: (context, index) {
              return ListTile(
                title: Text(pets[index].nombre),
                subtitle: Text(pets[index].especie),
                trailing: IconButton(
                  icon: Icon(Icons.delete, color: Colors.red),
                  onPressed: () => _service.deletePet(pets[index].id),
                ),
                onTap: () => _showForm(context, pets[index]),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () => _showForm(context, null),
      ),
    );
  }

  void _showForm(BuildContext context, Pet? pet) {
    // Aquí se implementaría el BottomSheet o Diálogo para Crear/Editar
    // Usando el skill-diseño para los inputs
  }
}
```

---

## 5. Verificación de Funcionalidad
Para asegurar que todo esté correcto:
1. **Terminal:** Ejecuta `flutter doctor` para verificar que no haya conflictos.
2. **Firebase:** Abre el simulador y verifica que al agregar una mascota en la app, aparezca instantáneamente en la **Console Firestore**.
3. **Logs:** Usa el `Debug Console` de VS Code para monitorear errores de conexión.

¿Deseas que profundice en el código de la **Página de Navegación** o en el script de **Scraping** para la carpeta de recursos del agente?
