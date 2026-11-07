# entregable-1-cicd

Taller Entregable 1 del curso de CI/CD: primera automatización con GitHub Actions.

## Integrantes

Texto de nombres usado en `AUTOR` y en el saludo del workflow: **John Jimenez**

| Nombre completo | Usuario de GitHub | Rol |
|---|---|---|
| John Jimenez | [@Johnjmz13](https://github.com/Johnjmz13) | Propietario del repositorio |

Modalidad: individual.

## Ejecución exitosa

- Workflow: [`.github/workflows/mi_workflow.yml`](.github/workflows/mi_workflow.yml)
- Script: [`mi_script.py`](mi_script.py)
- Ejecución exitosa (evento `push`): https://github.com/Johnjmz13/entregable-1-cicd/actions/runs/42388637669
- Ejecución exitosa manual (evento `workflow_dispatch`): https://github.com/Johnjmz13/entregable-1-cicd/actions/runs/42389340749

En los logs del job `imprimir_info` aparecen el saludo, la fecha/hora del runner (`$(date)`, en UTC) y las tres líneas del script: `Autor: John Jimenez`, `Sistema Operativo: Linux ...-azure` y la fecha/hora desde Python.

## Respuestas

### 1. ¿Qué eventos configuraste para disparar tu workflow y por qué?

Configuré dos eventos. El primero es `push` a la rama `main`: cada vez que subo cambios con `git push`, el workflow se ejecuta solo, sin que yo tenga que hacer nada más. Así lo que llega a `main` siempre queda probado. El segundo es `workflow_dispatch`, que agrega el botón "Run workflow" en la pestaña Actions. Lo probé y pude ejecutar el workflow otra vez sin cambiar ningún archivo, lo cual sirve para repetir una prueba o revisar que todo siga funcionando.

### 2. ¿Qué sistema operativo utiliza el runner `ubuntu-latest` y por qué puede ejecutar tu workflow aunque tu computadora sea Windows o macOS? ¿Qué relación tiene el runner con el shell de los pasos `run`?

El runner usa Linux: en mi ejecución salió `ubuntu-24.04`. Funciona aunque mi computadora sea Windows porque el workflow no se ejecuta en mi PC. Mi PC solo sube los archivos con `git push`; GitHub crea una máquina con Ubuntu en sus servidores, ahí corre los pasos y después la borra. Por eso mi script dijo "Windows" cuando lo corrí en mi PC y "Linux" cuando corrió en GitHub. Además, el runner define el shell de los pasos `run`: en Ubuntu es Bash y en Windows sería PowerShell.

### 3. Tres acciones del Marketplace de GitHub Actions no mencionadas en el material

Elegí tres que usaría en Zelena, el proyecto en el que trabajo (React y Node.js, con pagos en Stellar):

1. **[`actions/setup-node`](https://github.com/actions/setup-node)**: instala Node.js en el runner, como `setup-python` pero para JavaScript. La usaría para correr las pruebas del proyecto en cada push y enterarme si algo se rompió antes de desplegar.
2. **[`github/codeql-action`](https://github.com/github/codeql-action)**: revisa el código en busca de fallos de seguridad. Me sirve porque la aplicación maneja wallets y pagos.
3. **[`azure/login`](https://github.com/Azure/login)**: conecta el workflow con una cuenta de Azure para poder desplegar ahí. Usa secretos o credenciales temporales en vez de escribir contraseñas en el código.

### 4. ¿Qué es un secreto en GitHub Actions y para qué se usa? ¿Cómo lo usarías en tu workflow?

Un secreto es un dato sensible, como una contraseña o un token, que se guarda en la configuración del repositorio (*Settings > Secrets and variables > Actions*) y no en el código. Así el workflow puede usarlo sin que quede visible, lo cual es importante porque mi repositorio es público. En el workflow se usa con `${{ secrets.NOMBRE }}`. Por ejemplo, para desplegar Zelena en Azure con `azure/login`, guardaría los datos de conexión como secretos y los pasaría así:

```yaml
- uses: azure/login@v3
  with:
    client-id: ${{ secrets.AZURE_CLIENT_ID }}
    tenant-id: ${{ secrets.AZURE_TENANT_ID }}
    subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```

Algo parecido ya hacemos en Zelena: las claves de la base de datos se configuran en el panel del servidor y no en el repositorio.

### 5. Reflexión: ¿qué tareas automatizaría en mi trabajo diario y por qué?

Creo que es una herramienta muy útil para facilitar la integración y el despliegue del código. Actualmente en Zelena tenemos un monorepo desde donde atendemos a cada cliente. Usando GitHub Actions podemos desplegar de manera individual a cada cliente, al entorno que necesitemos y con las flags que requiera cada implementación. Esto nos facilita la vida y nos permite tener un mejor control y respuesta ante eventualidades o requerimientos del cliente.

Además, automatizaría que las pruebas se ejecuten en cada push, antes de cualquier despliegue, para enterarnos de un error antes de que le llegue a un cliente.
