# RHEL 9.4 no WSL 2 — requisito BV

**Versão obrigatória conforme tutorial corporativo:** Red Hat Enterprise Linux **9.4** (x86_64), sob WSL 2.

## Status do arquivo de instalação

**Não foi encontrado link direto validado para um arquivo oficial de RHEL 9.4 pronto para WSL 2.** A página abaixo inclui as ISOs Boot e DVD de RHEL 9.4, mas **nenhuma delas é um arquivo de importação WSL**:

- https://developers.redhat.com/products/rhel/download

A Red Hat anunciou imagens pré-compiladas oficiais de RHEL para WSL em maio de 2025. A documentação de suporte aponta uma imagem da versão 9.5; isso **não é uma imagem 9.4** e não satisfaz o requisito corporativo.

- Anúncio da Red Hat: https://developers.redhat.com/articles/2025/05/20/windows-subsystem-linux
- Informações de suporte e imagens: https://access.redhat.com/articles/7115538
- Como importar um rootfs compatível no WSL: https://learn.microsoft.com/pt-br/windows/wsl/use-custom-distro

## Como conseguir a versão exata

1. Recuperar o **arquivo de imagem ou URL exata** citado no tutorial da BV, ou solicitar a imagem de RHEL 9.4 à infraestrutura.
2. Verificar no notebook onde o RHEL 9.4 já está instalado se o pacote original ainda está em Downloads ou em uma pasta de instalação:

```cmd
dir /s /b "%USERPROFILE%\Downloads\*rhel*" 2>nul
wsl -l -v
wsl -d RHEL9.4 -- cat /etc/redhat-release
```

3. Não compartilhar uma exportação completa do WSL corporativo em repositórios públicos ou com terceiros sem autorização, porque pode conter certificados, tokens, arquivos internos e configurações particulares.

**Importante:** não tratar links de ISO, página de catálogo ou outra versão do RHEL como se fossem o endereço direto de um rootfs RHEL 9.4 para WSL.
