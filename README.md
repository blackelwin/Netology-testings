# DevOps-Netology

## Домашнее задание «2.4. Инструменты Git»

1. Команда git show aefea -s. Результат: полный хэш aefead2207ef7e2aa5dc81a34aedf0cad4c32545, комментарий коммита Update CHANGELOG.md
2. Команда git show 85024d3 -s. Результат: тег коммита v0.12.23
3. Комнада git show --pretty=format:' %P' b8d720. Результат: 2 родителя, 56cd7859e05c36c06b56d013b55a252d0bb7e158 9ea88f22fc6269854151c571162c5bcf958bee2b
4. Команда git log  v0.12.23..v0.12.24  --oneline. Результат: 
   	33ff1c03bb (tag: v0.12.24) v0.12.24
	b14b74c493 [Website] vmc provider links
	3f235065b9 Update CHANGELOG.md
	6ae64e247b registry: Fix panic when server is unreachable
	5c619ca1ba website: Remove links to the getting started guide's old location
	06275647e2 Update CHANGELOG.md
	d5f9411f51 command: Fix bug when using terraform login on Windows
	4b6d06cc5d Update CHANGELOG.md
	dd01a35078 Update CHANGELOG.md
	225466bc3e Cleanup after v0.12.23 release
5. Команда git log -S'func providerSource' --oneline. Результат:
	5af1e6234a main: Honor explicit provider_installation CLI config when present
	8c928e8358 main: Consult local directories as potential mirrors of providers
6. Команда git log -S'globalPluginDirs' --oneline. Результат:
	125eb51dc4 Remove accidentally-committed binary
	22c121df86 Bump compatibility version to 1.3.0 for terraform core release (#30988)
	35a058fb3d main: configure credentials from the CLI config file
	c0b1761096 prevent log output during init
	8364383c35 Push plugin discovery down into command package
7. Команда git log -S'synchronizedWriters' --oneline выдает результат: 
	bdfea50cc8 remove unused
	fd4f7eb0b9 remove prefixed io
	5ac311e2a9 main: synchronize writes to VT100-faker on Windows
   что очевидно, что в коммите 5ac311e2a9 функция synchronizedWriters была создана, в коммитах bdfea50cc8 и fd4f7eb0b9 была подредактирована.
	Команда git log -S'synchronizedWriters' --pretty=format:'%h - %an %ae' выдает результат:
		bdfea50cc8 - James Bardin j.bardin@gmail.com
		fd4f7eb0b9 - James Bardin j.bardin@gmail.com
		5ac311e2a9 - Martin Atkins mart@degeneration.co.uk
	что соответствует тому, что Martin Atkins - автор, James Bardin - редактор
