# ExamenArqDePC_HeiderMedina

Examen Final — Arquitectura Orientada a Servicios  
Ingeniería de Sistemas

---

## Estudiante

| Campo | Valor |
|---|---|
| Nombre completo | Heider Medina |
| Código estudiantil | 0192304 |
| Docente | Wilder Andrés Duarte Neira |
| Fecha | 18 de junio de 2026 |

---

## Objetivo del proyecto

Diseñar, versionar, desplegar y eliminar una máquina virtual en la nube utilizando AWS, CloudFormation y GitHub Actions, aplicando buenas prácticas de control de versiones mediante Git Flow y trabajo colaborativo mediante Pull Requests.

---

## Estructura del repositorio

```
ExamenArqDePC_NombreApellido/
├── .github/
│   └── workflows/
│       ├── deploy.yml       # Workflow de despliegue automático
│       └── destroy.yml      # Workflow de eliminación manual
├── template/
│   └── ec2.yaml             # Template CloudFormation (EC2 + Security Group)
└── README.md
```

---

## Tecnologías utilizadas

| Tecnología | Uso |
|---|---|
| **AWS CloudFormation** | Provisión de infraestructura como código (IaC) |
| **AWS EC2** | Máquina virtual Amazon Linux donde se sirve la página web |
| **AWS IAM** | Credenciales con permisos para operar CloudFormation desde GitHub |
| **GitHub Actions** | Automatización del despliegue y la eliminación de infraestructura |
| **GitHub Secrets** | Almacenamiento seguro de las credenciales de AWS |
| **Git Flow** | Estrategia de ramas para organizar el desarrollo del proyecto |
| **Apache HTTP Server** | Servidor web instalado en la EC2 mediante UserData |
| **HTML / CSS / JS** | Página web desplegada automáticamente en la instancia |

---

## Funcionalidad de Deploy

El workflow `.github/workflows/deploy.yml` se activa de dos formas:

- **Automáticamente** al hacer push a la rama `main` o `master`
- **Manualmente** desde la pestaña Actions → *Run workflow*

### Pasos que ejecuta

1. **Checkout** del repositorio en el runner de GitHub
2. **Configuración de credenciales AWS** usando los Secrets del repositorio
3. **Validación del template** con `aws cloudformation validate-template` — si hay errores de sintaxis, el pipeline se detiene aquí
4. **Despliegue del stack** con `aws cloudformation deploy`, lo que crea:
   - Una instancia EC2 Amazon Linux (`t2.micro`) en `us-east-1`
   - Un Security Group con el puerto 80 abierto al público
   - Una página web servida por Apache, instalada vía UserData
5. **Espera** a que el stack alcance el estado `CREATE_COMPLETE`
6. **Obtención de outputs**: extrae la IP pública de la EC2 y la imprime en el log

### Secrets requeridos en el repositorio

| Secret | Descripción |
|---|---|
| `AWS_ACCESS_KEY_ID` | ID de la clave de acceso IAM |
| `AWS_SECRET_ACCESS_KEY` | Clave secreta de acceso IAM |

---

## Funcionalidad de Destroy

El workflow `.github/workflows/destroy.yml` se activa **únicamente de forma manual** desde la pestaña Actions → *Run workflow*.

No tiene trigger automático para evitar eliminaciones accidentales de infraestructura.

### Pasos que ejecuta

1. **Checkout** del repositorio
2. **Configuración de credenciales AWS**
3. **Verificación** de que el stack existe antes de intentar eliminarlo
4. **Captura de la IP pública** actual para incluirla en el resumen final
5. **Eliminación del stack** con `aws cloudformation delete-stack`, lo que elimina en cascada:
   - La instancia EC2
   - El Security Group
6. **Espera** a que AWS confirme la eliminación completa
7. **Verificación** del estado final y resumen del resultado

---

## Objetivo del template EC2

El archivo `template/ec2.yaml` es un template de AWS CloudFormation escrito en YAML que define la infraestructura completa del proyecto como código.

### Recursos que despliega

**Security Group (`ExamenSecurityGroup`)**  
Controla el tráfico de red de la instancia. Permite:
- Puerto **80** (HTTP) desde cualquier IP — para acceder a la página web
- Puerto **22** (SSH) desde cualquier IP — para acceso de laboratorio

**Instancia EC2 (`ExamenEC2Instance`)**  
Máquina virtual con las siguientes características:
- Sistema operativo: Amazon Linux 2
- Tipo: `t2.micro` (capa gratuita de AWS)
- Región: `us-east-1`
- UserData: script bash que se ejecuta al iniciar la instancia, instala Apache y despliega el `index.html`

### Página web desplegada

La página muestra:
- Nombre completo del estudiante
- Código estudiantil
- Texto: *Examen Final Arquitectura de Computadores*
- Reloj en tiempo real (zona horaria Colombia)
- Indicador de estado de la instancia

### Outputs del template

| Output | Descripción |
|---|---|
| `InstanceId` | ID único de la instancia EC2 |
| `PublicIP` | IP pública para acceder a la página web |
| `PublicDNS` | DNS público de la instancia |
| `WebsiteURL` | URL completa: `http://<IP>` |
| `SecurityGroupId` | ID del Security Group creado |

---

## Flujo Git Flow aplicado

```
main
 └── feature/XX_DeployEC2       → deploy.yml
 └── feature/XX_DestroyEC2      → destroy.yml
 └── feature/XX_TemplateEC2     → template/ec2.yaml
 └── feature/XX_ReadmeProject   → README.md
 └── feature/XX_ColaboradorPR   → modificación UserData (compañero)
```

Cada rama se integra a `main` mediante un Pull Request con revisión y Merge.

---

## Evidencias del proyecto

### Parte 1 — Preparación
_Captura del repositorio creado en GitHub y los Secrets configurados_

### Parte 2 — Construcción
_Captura de los archivos creados en cada rama feature_

### Parte 3 — Git Flow
_Captura de los commits y las ramas en GitHub_

### Parte 4 — Trabajo colaborativo
_Captura del Pull Request del compañero y del Merge realizado_

### Parte 5 — Despliegue
_Captura del workflow exitoso, stack en CloudFormation, EC2 creada y página web funcionando_

### Parte 6 — Eliminación
_Captura del workflow de destroy exitoso y stack eliminado en CloudFormation_

---

## Comandos Git utilizados

```bash
# Clonar el repositorio
git clone https://github.com/usuario/ExamenArqDePC_NombreApellido.git

# Crear rama feature
git checkout -b feature/XX_DeployEC2

# Agregar cambios
git add .

# Hacer commit
git commit -m "feat: agregar workflow de despliegue deploy.yml"

# Subir rama al repositorio remoto
git push origin feature/XX_DeployEC2

# Volver a main y hacer merge local (opcional, se hace por PR en GitHub)
git checkout main
git merge feature/XX_DeployEC2
git push origin main
```

---

*Examen Final — Arquitectura Orientada a Servicios — Ingeniería de Sistemas*
