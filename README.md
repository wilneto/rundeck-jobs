# Rundeck Jobs – Git manda 🚀

Este repositório é a **fonte única de verdade (SSOT)** para os **jobs do Rundeck**.  
Toda alteração em jobs é feita via Git e sincronizada automaticamente para o Rundeck por meio de **GitHub Actions + Rundeck CLI (`rd`)**.

> **Importante:**  
> - **Projetos são infraestrutura**
> - **Jobs são código**
>
> Este repositório **não cria projetos no Rundeck automaticamente**.  
> Projetos devem existir previamente.

### 🔄 Fluxo recomendado de uso

1.	Criar projeto no Rundeck (UI ou bootstrap inicial)
2.	Criar pasta <projeto>/jobs/ no repositório
3.	Versionar jobs em YAML
4.	Fazer push na main
5.	Pipeline sincroniza automaticamente 🚀


### 📌 Resumo

✔ Git é a fonte da verdade.  
✔ Pipeline sincroniza apenas o que mudou.  
✔ CLI-only (rd).  
✔ Sem efeitos colaterais.  
✔ Seguro, previsível e auditável.  

## 📂 Estrutura do Repositório

A estrutura segue o padrão:

/
└── jobs/  
├── job-1.yaml  
├── job-2.yaml  
└── …  

### Exemplo real:
LBaaS/jobs/
Mgc-backup/jobs/
PerformanceTest/jobs/
monitoring/jobs/
iaas/jobs/

- O **nome da pasta de primeiro nível** corresponde **exatamente** ao nome do projeto no Rundeck.
- Apenas arquivos dentro de `*/jobs/*.yml` ou `*/jobs/*.yaml` são considerados pela pipeline.



## ⚙️ Como funciona a sincronização

A sincronização acontece automaticamente via **GitHub Actions** quando:

- Um arquivo `*.yml` ou `*.yaml` dentro de `*/jobs/` é alterado
- Um push é feito na branch `main`
- Ou o workflow é executado manualmente (`workflow_dispatch`)

### O que a pipeline faz

1. Detecta **apenas os jobs alterados** no commit
2. Identifica o projeto pelo nome da pasta
3. Valida se o projeto **existe no Rundeck**
4. Importa ou atualiza o job usando:
```
   rd jobs load --duplicate update
```

### O que a pipeline não faz

❌ Criar projetos no Rundeck.  
❌ Alterar configurações de projeto.  
❌ Apagar jobs automaticamente.  

Essas ações são intencionais para manter segurança e previsibilidade.

### 🧱 Pré-requisitos

No Rundeck
	•	O projeto deve existir previamente.   
	•	O token configurado deve ter permissão para:    
	•	read projetos.   
	•	import jobs.   

### No GitHub (Secrets)

Configure os secrets no repositório:


| Secret| Descrição |
|-------------|-------------|
| RUNDECK_URL      | URL base do Rundeck (ex: https://rundeck.exemplo.com)     |
| RUNDECK_TOKEN     | Token de API com permissão para importar jobs      |



### 🧪 O que acontece se o projeto não existir?
```
iaas/jobs/resize_dbaas.yaml
```

Se o projeto iaas não existir no Rundeck, a pipeline:

	•	Não falha abruptamente.  
	•	Pula o job. 
	•	Exibe um aviso claro no log:  

```
Projeto 'iaas' não existe no Rundeck.
Crie o projeto e faça push novamente.
```

Depois que o projeto for criado (UI ou bootstrap), basta fazer um novo push.

### 📝 Formato esperado dos Jobs (YAML)

	•	O arquivo deve estar no formato compatível com exportação do Rundeck
	•	Recomenda-se sempre exportar um job pela UI e usar como base
	•	Campos importantes que não devem faltar:
	•	dispatch.strategy
	•	sequence.strategy
	•	Estrutura em lista (- name: ...)

### Dica 💡

Para gerar um modelo válido:

rd jobs export -p <projeto> -f yaml > modelo.yaml

### 🔐 Por que não usar API para criar projetos?

Decisão arquitetural consciente:
	•	Evita permissões administrativas no token
	•	Evita dependência de versão da API
	•	Evita drift silencioso
	•	Mantém blast radius mínimo
	•	Separa claramente infra de código

Projeto é criado uma vez.
Job evolui sempre.

⸻

