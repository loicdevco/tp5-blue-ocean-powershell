pipeline {
agent any
options {
skipDefaultCheckout(true)
timestamps()
disableConcurrentBuilds()
buildDiscarder(logRotator(numToKeepStr: '10'))
}
environment {
BUILD_DIR = 'build-output'
EXPECTED_VERSION = '1.0.0'
}
stages {
stage('Informations') {
steps {
powershell '''
$ErrorActionPreference = 'Stop'
Write-Host "Machine : $env:COMPUTERNAME"
Write-Host "Compte : $([System.Security.Principal.Windows
Write-Host "Job : $env:JOB_NAME"
Write-Host "Build : $env:BUILD_NUMBER"
Write-Host "Workspace : $env:WORKSPACE"
Write-Host "PowerShell : $($PSVersionTable.PSVersion)"
'''
}
}
stage('Checkout') {
steps {
checkout scm
}
}
stage('Préparation') {
steps {
powershell '''
$ErrorActionPreference = 'Stop'

Write-Host 'Préparation du projet'
New-Item -ItemType Directory -Path 'build-output' -Force
if (-not (Test-Path 'application.txt')) {
Write-Error 'Le fichier application.txt est introuvab
}
Copy-Item 'application.txt' 'build-output\\application.tx
Write-Host 'Fichier copié dans build-output.'
'''
}
}
stage('Validation') {
steps {
powershell '''
$ErrorActionPreference = 'Stop'
$content = Get-Content 'build-output\\application.txt' -R
if ([string] :IsNullOrWhiteSpace($content)) {
throw 'Le fichier application.txt est vide.'
}
if ($content -notmatch 'Application Blue Ocean PowerShell
throw 'Le nom attendu de l’application est absent.'
}
if ($content -notmatch "Version\s*:\s*$env:EXPECTED_VERSI
throw "La version attendue est absente : $env:EXPECTE
}
Write-Host "Validation réussie pour la version $env:EXPEC
'''
}
}
stage('Test') {
steps {
powershell '''
$ErrorActionPreference = 'Stop'

$file = 'build-output\\application.txt'
if (-not (Test-Path $file)) {
throw 'Le test a échoué : le fichier préparé est abse
}
$lineCount = @(Get-Content $file).Count
if ($lineCount -lt 2) {
throw 'Le test a échoué : le fichier doit contenir au
}
Write-Host "Test réussi : $lineCount lignes vérifiées."
'''
}
}
stage('Rapport') {
steps {
powershell '''
$ErrorActionPreference = 'Stop'
$report = @(
"Nom du job : $env:JOB_NAME"
"Numéro du build : $env:BUILD_NUMBER"
"Date : $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')"
'Résultat : les validations sont réussies'
)
$report | Set-Content 'build-output\\build-report.txt' -E
Get-Content 'build-output\\build-report.txt'
'''
}
}
}
post {
success {
powershell '''
Write-Host "Pipeline réussi : $env:JOB_NAME #$env:BUILD_NUMBE
'''
}

failure {
powershell '''
Write-Host 'Pipeline en échec : consulter la Console Output.'
'''
}
}
}
