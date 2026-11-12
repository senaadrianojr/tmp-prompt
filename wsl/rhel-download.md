# RHEL 9.4 para WSL 2 — referência de download

**Versão exigida pelo tutorial corporativo: Red Hat Enterprise Linux 9.4 (x86_64).** Não substituir por 9.6, 9.8, 9.9 ou 10 sem orientação da equipe responsável.

## Links oficiais
- Downloads da Red Hat (inclui a seção **Red Hat Enterprise Linux 9.4**): https://developers.redhat.com/products/rhel/download
- Instruções oficiais de RHEL no WSL: https://developers.redhat.com/articles/2025/05/20/getting-started-rhel-windows-subsystem-linux
- Importação de distribuições personalizadas no WSL (Microsoft): https://learn.microsoft.com/en-us/windows/wsl/use-custom-distro

## Atenção ao tipo de imagem

Na seção de **RHEL 9.4** da página de downloads aparecem **Boot ISO** e **DVD ISO** (x86_64/aarch64). Esses arquivos ISO são instaladores tradicionais, **não** imagens rootfs prontas para importar com `wsl --import`.

Para o WSL 2, é necessário um pacote compatível (por exemplo, `.tar`, `.tar.gz` ou `.wsl` adequado ao WSL) da **mesma versão 9.4**. Não foi confirmada, na página de downloads, uma imagem WSL2 pré-compilada especificamente para 9.4.

**Caminho recomendado no ambiente corporativo:** consultar o tutorial e a equipe de infraestrutura para obter a origem autorizada do pacote RHEL 9.4 usado na instalação original.

## Verificar versão no CMD

```cmd
wsl -l -v
wsl -d RHEL9.4 -- cat /etc/redhat-release
```

Se existir uma imagem WSL 2 limpa e aprovada, o padrão de importação no **CMD** é:

```cmd
mkdir C:\WSL\RHEL9.4
wsl --import RHEL9.4 C:\WSL\RHEL9.4 C:\caminho\imagem-rhel-9.4.tar --version 2
```

Substitua o caminho de exemplo pelo pacote efetivamente fornecido. A importação exige que o WSL 2 esteja habilitado e que o arquivo seja realmente compatível.

**Segurança:** não distribuir exportações da distribuição corporativa já configurada sem autorização, pois podem conter arquivos, certificados, tokens e outros dados internos.
