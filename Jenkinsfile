pipeline{
    agent any

    environment {
        RepositoryEkspert = "E:/git/Ekspert_master"
        SonarScanner = "E:/SonarScanner/bin/sonar-scanner.bat"
        RepositoryOneC = "E:/Хранилища/Эксперт"
        RepositoryOneCUser = "ТолькоПросмотр"
        RepositoryOneCPass = "1111"
        BaseConnection = "/Slocalhost\\Ekspert_Test"
        Version1c = "8.3.22.1750"
        XMLOneC = "E:/git/Ekspert_master/src/cf"
        EDTWorkspace = "E:\\EDT\\workspace"
        EDTProject = "E:\\EDT\\Ekspert_master"
        EDTLocation = "E:\\1cedtstart\\1C_EDT_2022.2_\\1cedt"
        VersionOld = ""
        VersionNew = ""
        ClasterAddres = "localhost:1545"
        BaseTest = "Ekspert_Test"
    }
stages{
    stage("Помещение в гит"){
        steps{
                script {
                    VersionOld = readFile """${XMLOneC}/VERSION"""
                    bat """chcp 65001\n gitsync --v8version ${Version1c} --ibconnection ${BaseConnection} sync -u ${RepositoryOneCUser} -p ${RepositoryOneCPass} ${RepositoryOneC} ${XMLOneC}"""
                    VersionNew = readFile """${XMLOneC}/VERSION"""
                }
            }
        }
    stage("Обновление тестовой базы"){
        steps{
            script {
                    if(VersionOld != VersionNew){
                        bat """chcp 65001\n vrunner scheduledjobs lock --ras ${ClasterAddres} --db ${BaseTest}"""
                        bat """chcp 65001\n vrunner session kill --ras ${ClasterAddres} --db ${BaseTest} --uccode 1111"""
                        bat """chcp 65001\n vrunner updatedb --uccode 1111 --nocacheuse --ibconnection ${BaseConnection}"""
                        bat """chcp 65001\n vrunner scheduledjobs unlock --ras ${ClasterAddres} --db ${BaseTest}"""
                        bat """chcp 65001\n vrunner session unlock --ras ${ClasterAddres} --db ${BaseTest} --uccode 1111"""
                    }
                }
            }
        }
    stage("Конвертация в ЕДТ"){
        steps{
                script {
                    if(VersionOld != VersionNew){
                        if (fileExists("${EDTWorkspace}")) {
                                bat """chcp 65001\n rd /s /q ${EDTWorkspace}"""
                            }
                        if (fileExists("${EDTProject}")) {
                                bat """chcp 65001\n rd /s /q ${EDTProject}"""
                            }
                        bat """chcp 65001\n ring edt@2022.2.3 workspace import --configuration-files ${RepositoryEkspert}/src/cf --project ${EDTProject} --workspace-location ${EDTWorkspace} --edt-location ${EDTLocation}"""
                    }
                }
            }
        }
    stage("Запуск сонара"){
        steps{
            script {
                    if(VersionOld != VersionNew){
                        try {
                            bat """chcp 65001\n cd /d ${RepositoryEkspert} && ${SonarScanner}"""
                        } catch (err) {
                            echo "Caught: ${err}"
                        }
                    }
                }
            }
        }
    stage("Дымовые тесты"){
            steps{
                bat "chcp 65001\n vrunner  xunit"
            }
        }
    }
    post{
        always{
            allure includeProperties: false,results: [[path: 'report']]
        }
    }
}