# Examen 2º Trimestre

## Deploy CI/CD
1. Crear un repositorio en GitHub
2. Crear una carpeta llamada `.github/workflows`
    2.1. Dentro de la carpeta crear un archivo llamado deploy.yml:

```yaml
name: Deploy to CI/CD
on:
push:
    branches:
    - main

jobs:
deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
    uses: actions/checkout@v2

    - name: Install sshpass
    run: sudo apt-get install -y sshpass

    - name: Create .ssh directory
    run: mkdir -p ~/.ssh

    - name: Add remote host to known_hosts
    run: ssh-keyscan -H ${{ secrets.SSH_HOST }} >> ~/.ssh/known_hosts
    - name: Copy files via SCP
    env:
        SSHPASS: ${{ secrets.SSH_PASSWORD }}
    run: sshpass -e scp -r ./ ${{ secrets.SSH_USER }}@${{ secrets.SSH_HOST }}:/root/website

    - name: Execute command on VPS
    env:
        SSHPASS: ${{ secrets.SSH_PASSWORD }}
    run: sshpass -e ssh ${{ secrets.SSH_USER }}@${{ secrets.SSH_HOST }} "pm2 stop test-server && cd website && npm install && pm2 start test-server &"
```

3. Subir el flujo de trabajo
```bash
git add .
git commit -m "Agregar flujo de trabajo CI"
git push 
```

4. Añadir secretos en GitHub
    1. Accede a tu repositorio en GitHub.

    2. En la barra de menú de tu repositorio, haz clic en Settings (Configuración).

    3. En el menú de la izquierda, desplázate hacia abajo hasta encontrar la sección Secrets.

    4. Haz clic en Secrets y luego selecciona New repository secret.
        - Aquí agregarás los secretos que usas en tu archivo YAML, como las credenciales SSH (SSH_HOST, SSH_USER, SSH_PASSWORD, etc.).

5. Verificar que todo este correcto