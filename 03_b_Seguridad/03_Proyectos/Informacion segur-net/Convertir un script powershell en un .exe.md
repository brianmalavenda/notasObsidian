tipo::
tags::
fecha:: 2026-09-17
links::
links_externos::

Para convertir tu script `.ps1` en un archivo `.exe` que el investigador pueda ejecutar con un simple doble clic, la herramienta estándar y gratuita es **PS2EXE**.

**Pasos para crear el ejecutable:**

1. **Instala el módulo PS2EXE**: Abre PowerShell como administrador y ejecuta:
    
    ```powershell
    Install-Module ps2exe -Force
    ```
    
2. **Prepara tu script**: Guarda tu script de verificación (el que compara los hashes) en un archivo, por ejemplo, `VerificarIntegridad.ps1`. Asegúrate de que el script incluya una lógica que muestre un mensaje claro al final (por ejemplo, usando `Write-Host` con colores).
    
3. **Compila el script a ejecutable**: Ejecuta el siguiente comando en PowerShell:
    
    ```powershell
    Invoke-ps2exe -inputFile .\VerificarIntegridad.ps1 -outputFile .\VerificarIntegridad.exe -noConsole -title "Verificador de Integridad" -description "Herramienta para verificar la integridad de los archivos de investigación" -company "Universidad" -product "Verificador de Integridad" -version "1.0.0.0"
    ```


---
#### Referencias

