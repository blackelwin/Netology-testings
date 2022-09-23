# DevOps-Netology

## Домашнее задание к занятию "3.1. Работа в терминале, лекция 1"

1. ВМ выделены по умолчанию 1024мб оперативной памяти и 2 процессора

2. Для задания оперативной памяти и процессоров используется командный блок config.vm.provider:
	
  ```
  config.vm.provider "virtualbox" do |v|
    v.memory = 1024 #здесь задается объем оперативной памяти
    v.cpus = 2 #здесь задается количество доступных процессоров
  end
  ```
3. Длина журнала History задается двумя командами: HISTSIZE и HISTFILESIZE:
	
	HISTSIZE описан на 1120 строчке;
	
	HISTFILESIZE описан на 1124 строчке;

4. Директива ignoreboth это опция команды HISTCONTROL, которая определяет каким образом список команд сохраняются в журнале History. ignoreboth объединяет и использует директиву ignorespace, которая не сохраняет строки начинающиеся с пробела, и директиву ignoredups, которая не сохраняет строки, совпадающие с последней выполненной командой.
5. {} - зарезервированные слова и должны появляться там, где зарезервированные слова должны быть опознаны. Используются для обозначения списка, группы команд. {} и список должны быть разделены пробелами или другими метасимволами оболочки. Описано на 344 строчке. 
Так же на строчке 1499 описано, что для использования { вне списка, необходимо использовать "обратный слэш - \", чтобы оболочка не приняла как начало описания списка, группы команд.
Так же на строчке 1464 описан механизм раскрытия скобок в командах, например цикличное выполнение команды с подстановкой.
6. touch {1..10000}

   При выполнении команды touch {1..300000} выдает ошибку - слишком большой список аргументов.

7. [[]] конструкция используется для обозначения выражений и на выходе выдает 1 или 0 в зависимости от условий выражения, удобно использовать в скриптах.

   [[ -d /tmp ]] проверяет наличие папки /tmp

8. mkdir /tmp/new_path_dir/

   cp /bin/bash /tmp/new_path_dir/

   PATH=/tmp/new_path_dir/:$PATH
   
9. at используется для запуска одноразовых задач в указанное время; 
   
   batch используется для запуска одноразовых задач, когда загрузка системы падает ниже 0,8  
