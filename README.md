# DevOps-Netology

## Начинаем изучение GIT

## Добавлен файл игнорирования /terraform/.gitignore.
Список игнорируемых файлов и папок, указанных в /terraform/.gitignore:
 - директория .terraform;
 - файлы состояния формата .tfstate и .tfstate.;
 - файл ошибок crash.log и файлы содержащие crash.*.log;
 - файлы, хранящие пароли, закрытые ключи и другие секретные данные формата .tfvars и tfvars.json
 - файлы override.tf, override.tf.json, *_override.tf и *_override.tf.json;
 - файл конфигурации CLI terraform.rc и файлы формата .terraformrc

Указанные файлы и директории будут игнорированы и не включены в коммит.
