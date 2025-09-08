pipeline {
    agent any
    stages {
        stage('Installer Node.js si il existe pas') {
            steps {
                bat '''
                @echo off
                node -v 
                if %ERRORLEVEL% NEQ 0 (
                    echo "Node.js non trouvé, installation via Chocolatey..."
                    powershell -Command "Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))"
                    choco install nodejs-lts -y
                ) else (
                    echo "Node.js déjà installé"
                )
                '''
            }
        }

        stage('Installer Newman si il existe pas') {
            steps {
                bat '''
                @echo off
                newman -v 
               if %ERRORLEVEL%==0 (
                     echo "Newman déjà installé"
                     ) else (
                     echo "Newman non trouvé → installation..."
                     npm install -g newman newman-reporter-htmlextra
                     )
                '''
            }
        }
        stage('Récupéré le repot git') {
            steps {
               git branch: 'main', url: 'https://github.com/LyndaH08/TestAPI.git'
            }
        }

        stage('Exécuter la collection Postman') {
            steps {
                bat 'newman run TestRapport2.postman_collection.json'
           }
        }}
    post { //actions à exécuter après les stages.
        always {
             // Publie le rapport HTML directement dans Jenkins
              publishHTML(target: [ //Une fonction qui affiche un rapport html dans jenkins
                                 //Target : les options de configurations
                reportName: 'Rapport',          // Nom visible dans Jenkins
                reportDir: '',            // Dossier contenant le HTML
                reportFiles: 'results.html',    // Fichier à afficher
                keepAll: true,  //Garder tous les rapports de tous les builds
                allowMissing: true, //Meme si le rapport n'existe pas le build est OK
                alwaysLinkToLastBuild : true
                   ])
            //sauvegarder des fichiers du workspace pour qu’ils 
            //soient visibles et téléchargeables apres le build
             //archiveArtifacts artifacts: 'newman/results.html'
        }
    }
     
    
}
