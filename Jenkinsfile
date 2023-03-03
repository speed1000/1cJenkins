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
    }
    post{
        failure{
            echo "Конвейер завершился с ошибкой"
        }
        success{
            echo "Конвейер выполнен"
        } 
    }
stages{ 
    stage("Помещение в гит"){
        steps{
                bat """chcp 65001\n gitsync --v8version ${Version1c} --ibconnection ${BaseConnection} sync -u ${RepositoryOneCUser} -p ${RepositoryOneCPass} ${RepositoryOneC} ${XMLOneC}"""
            }
        post{
            failure{
                echo "Помещение в гит провалено"
            }
            success{
                echo "Помещение в гит успешно выполнено"
                }
            }
        }
    stage("Конвертация в ЕДТ"){
        steps{
                script {
                    if (fileExists("${EDTWorkspace}")) {
                            bat """chcp 65001\n rd /s /q ${EDTWorkspace}"""
                        }
                    if (fileExists("${EDTProject}")) {
                            bat """chcp 65001\n rd /s /q ${EDTProject}"""
                        }
                }
                bat """chcp 65001\n ring edt@2022.2.3 workspace import --configuration-files ${RepositoryEkspert}/src/cf --project ${EDTProject} --workspace-location ${EDTWorkspace} --edt-location ${EDTLocation}"""
            }
        post{
            failure{
                echo "Конвертация в ЕДТ провалена"
                }
            success{
                echo "Конвертация в ЕДТ выполнена"
                }
            }
        }
    stage("Запуск сонара"){
        steps{
                bat """chcp 65001\n cd /d ${RepositoryEkspert} && ${SonarScanner}"""
            }
        post{
            failure{
                echo "Проверка сонара провалена"
                }
            success{
                echo "Проверка сонара выполнена"
                    }
            }
        }
    }
}