pipeline {
    agent any
     parameters {
        choice(name: 'ENV', choices: ['pp', 'test','QA'], description: 'Choisir un environnement')
    }
    stages {
       //stage('Run') {
         //   steps {
            //    echo "J’exécute avec: ${params.ENV}"
          //  }
       // }
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
                bat 'newman run TestRapport2.postman_collection.json --reporters=cli,htmlextra --reporter-htmlextra-export newman/results.html'
           }
        }}
    post { //actions à exécuter après les stages.
        always {
             // Publie le rapport HTML directement dans Jenkins
              publishHTML(target: [ //Une fonction qui affiche un rapport html dans jenkins
                                 //Target : les options de configurations
                reportName: 'Rapport',          // Nom visible dans Jenkins
                reportDir: 'newman',            // Dossier contenant le HTML
                reportFiles: 'results.html',    // Fichier à afficher
                keepAll: true,  //Garder tous les rapports de tous les builds
                allowMissing: true, //Meme si le rapport n'existe pas le build est OK
                   ])
            //sauvegarder des fichiers du workspace pour qu’ils 
            //soient visibles et téléchargeables apres le build
             //archiveArtifacts artifacts: 'newman/results.html'
			 
			     emailext(
             to: 'hachemilindaa@gmail.com',
            subject: "Build terminé : ${currentBuild.fullDisplayName}",
    body: """
    <html>
      <body>
        <h2>Jenkins Build Report</h2>
        <p><strong>Projet :</strong> ${JOB_NAME}</p>
        <p><strong>Build # :</strong> ${BUILD_NUMBER}</p>
        <p><strong>Status :</strong> ${currentBuild.currentResult}</p>
        <p>Consultez le rapport complet ici : <a href="${BUILD_URL}newman/results.html">Rapport Newman</a></p>
        <p>Date et heure : ${new Date()}</p>
      </body>
    </html>
    """,
    mimeType: 'text/html',
        )
        }
    }
     
    
}
