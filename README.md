# Exercício EBAC — Configuração do Logstash

Este diretório contém uma configuração de exemplo do Logstash para receber logs de uma aplicação backend, interpretar registros JSON ou texto simples e enviá-los ao Elasticsearch.

## Arquivos

- `logstash.conf`: configuração principal solicitada no exercício.
- `diagrama.mmd`: diagrama Mermaid do fluxo de processamento.

## Fluxo

1. **Input:** lê o arquivo `/logs/app.log`.
2. **Multiline:** reúne linhas pertencentes ao mesmo registro JSON quando necessário.
3. **Filter — JSON:** interpreta registros JSON e coloca os campos em `dados`.
4. **Filter — Grok:** para registros em texto simples, extrai timestamp, nível e mensagem.
5. **Filter — Date:** converte o timestamp para o campo padrão `@timestamp`.
6. **Output:** envia os eventos ao índice `tarefas-logs` no Elasticsearch e também os exibe no terminal.

## Integração com Docker Compose

A configuração pressupõe que o Logstash esteja em uma rede Docker na qual o serviço Elasticsearch possa ser acessado pelo nome `elasticsearch`, na porta `9200`.

O arquivo de log precisa estar disponível dentro do container em `/logs/app.log`. No projeto de referência, isso pode ser feito montando a pasta local `logs` no container como `/logs`.

## Atenção ao formato do timestamp

Para registros JSON, a configuração procura o campo `timestamp` dentro do objeto JSON (`dados.timestamp`). Ela aceita timestamps no formato `yyyy-MM-dd HH:mm:ss,SSS` e ISO 8601.

Se a aplicação usar outro nome de campo (por exemplo, `asctime` ou `time`), ajuste `[dados][timestamp]` no filtro `date` para o nome real. O filtro Grok considera linhas de texto simples no formato:

```text
2026-11-05 20:01:55,311 ERROR mensagem do log
```

## Como conferir

Com o Elasticsearch em execução e o Logstash usando este arquivo, consulte:

```powershell
curl.exe http://localhost:9200/tarefas-logs/_count
```

