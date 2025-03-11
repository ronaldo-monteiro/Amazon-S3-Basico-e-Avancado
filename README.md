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

```provider "aws" {
  region = "us-east-1"
}

# Recurso do Bucket S3
resource "aws_s3_bucket" "example" {
  bucket = "ronaldo-monteiro-bucket-lab3"

  tags = {
    Name        = "rronaldo-monteiro-bucket-lab3"
    Environment = "Devops"
  }
}

# Recurso de Versionamento no S3
resource "aws_s3_bucket_versioning" "example_versioning" {
  bucket = "ronaldo-monteiro-bucket-lab3"
  versioning_configuration {
    status = "Enabled" 
  }
}

# Recurso para fazer upload de arquivo no S3
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
      storage_class = "GLACIER_IR" # Glacier Instant Retrieval
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





> **Nota:** As próximas etapas abordarão como configurar o versionamento, ciclo de vida e URLs pré-assinadas para garantir segurança e eficiência no armazenamento de arquivos no Amazon S3.

