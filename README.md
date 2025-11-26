name: CI Pipeline

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
      # 1. Checkout del código
      - name: Checkout code
        uses: actions/checkout@v4

      # 2. Instalar Node.js (versión 18 en este ejemplo)
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 18

      # 3. Instalar dependencias
      - name: Install dependencies
        run: npm install

      # 4. Ejecutar pruebas
      - name: Run tests
        run: npm test

      # 5. Generar reporte (ej. cobertura con Jest)
      - name: Generate coverage report
        if: success()  # Solo si las pruebas pasan
        run: npm run coverage

      # 6. Subir reporte como artefacto
      - name: Upload coverage artifact
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: coverage-report
          path: coverage/
  
      # 7. Verificar resultado final del pipeline
      - name: Verify pipeline status
        if: failure()
        run: |
          echo "❌ El pipeline falló."
          exit 1

      - name: Pipeline success
        if: success()
        run: echo "✅ Pipeline completado correctamente."

