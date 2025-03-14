# Amazon S3 - Armazenamento, Segurança e versionamento.

## Recursos Utilizados

 **Amazon S3**:
- **Versionamento:** Manter um histórico de versões dos arquivos.
- **Ciclo de Vida:** Automatizar a movimentação de arquivos entre diferentes classes de armazenamento.
- **URLs Pré-assinadas:** Gerar links temporários para acesso seguro a objetos específicos.

## Cenário
Uma empresa e precisa garantir que os dados estejam armazenados de forma segura, organizada e com custos otimizados.

## Construir toda a infraestrutura via Terraform

- Arquivos de Teste: Criaremos um arquivo de texto simples para os testes de versionamento
- Permissões no IAM.

## Tarefas
### 1. Criação do Bucket (Repositório de Arquivos)

#### 2 Segue o código via terraform:

```hcl 
provider "aws" {
  region = "us-east-1"
}


resource "aws_s3_bucket" "example" {
  bucket = "ronaldo-monteiro-bucket-lab3"

  tags = {
    Name        = "rronaldo-monteiro-bucket-lab3"
    Environment = "Devops"
  }
}


resource "aws_s3_bucket_versioning" "example_versioning" {
  bucket = "ronaldo-monteiro-bucket-lab3"
  versioning_configuration {
    status = "Enabled" 
  }
}


resource "aws_s3_object" "example_file" {
  bucket = aws_s3_bucket.example.bucket                           
  key    = "teste.txt"                                            
  source = "/home/ronaldo/Turma DPCN 10/LAB03/teste.txt"          
  acl    = "private"                                              
  etag   = filemd5("/home/ronaldo/Turma DPCN 10/LAB03/teste.txt") 
}


resource "aws_s3_bucket_lifecycle_configuration" "example" {
  bucket = aws_s3_bucket.example.bucket 

  rule {
    id     = "MoverParaGlacierApos30Dias"
    status = "Enabled"

    transition {
      days          = 30
      storage_class = "GLACIER_IR" 
    }

    expiration {
      days = 31
    }

    noncurrent_version_expiration {
      noncurrent_days = 31
    }

    noncurrent_version_transition {
      noncurrent_days = 30
      storage_class   = "GLACIER_IR"
    }
  }
}
```



> **Seguem os Resultados:** 

**Bucket criado**

<p align="center">
  <img src="instancia.png" alt="Segundo Exemplo do código!">
</p>


**Bloqueio de acesso publico** 

<p align="center">
  <img src="block-public-access.png" alt="Segundo Exemplo do código!">
</p>


**Versiomanto do arquivo** 

<p align="center">
  <img src="versionamento.png" alt="Segundo Exemplo do código!">
</p>


**Acesso disponível** 

<p align="center">
  <img src="acessodisponivel.png" alt="Segundo Exemplo do código!">
</p>


**Acesso negado após um minuto** 

<p align="center">
  <img src="acessonegado.png" alt="Segundo Exemplo do código!">
</p>


## Concluindo o projeto


 ### Conceitos Importantes

- **Organização**: Como os buckets funcionam como contêineres para seus arquivos.
- **Segurança**: A importância de bloquear o acesso público e como gerar URLs pré-assinadas para compartilhamento seguro.
- **Proteção de Dados**: Como o versionamento permite recuperar versões anteriores de arquivos.
- **Otimização de Custos**: Como as regras de ciclo de vida ajudam a mover dados para classes de armazenamento mais econômicas, como o S3 Glacier.

 ## Ao final, Destruindo tudo com um simples terraform Destroy!
