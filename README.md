# SD-ControleConcorrenciaJava
ANÁLISE DAS 4 IMPLEMENTAÇÕES DE CONTROLE DE CONCORRÊNCIA EM JAVA

Este projeto demonstra o comportamento de concorrência em um sistema bancário simples com múltiplas threads realizando saques simultâneos em uma mesma conta. Foram analisadas quatro abordagens:

Concorrente (sem sincronização)

Synk (synchronized)

Lock (java.util.concurrent.locks.Lock)

RwLock (ReentrantReadWriteLock)

Cada implementação foi executada 3 vezes, e os prints estão na pasta /prints.

 1. Concorrente (SEM SINCRONIZAÇÃO)
➤ Comportamento observado:

Todos os saques dão saldo insuficiente, mesmo com saldo inicial.

O saldo em algumas execuções chega a 0 antes mesmo das threads tentarem sacar.

Há perda de integridade de dados.

➤ Causa:

Sem sincronização, várias threads verificam o saldo ao mesmo tempo, e o saldo pode ser lido de maneira inconsistente.

➤ Conclusão:

⚠ Condição de corrida extremamente grave.
⚠ Não garante exclusão mútua.

 2. Synk (uso de synchronized)
➤ Comportamento observado:

Apenas uma thread consegue sacar (quase sempre o Pai).

As outras recebem saldo insuficiente.

O saldo nunca fica negativo e nunca é lido incorretamente.

➤ Causa:

synchronized garante exclusão mútua:
→ apenas uma thread acessa o método de saque por vez.

➤ Conclusão:

✔ Seguro
✔ Evita condições de corrida
✖ Baixa eficiência, pois mesmo operações de leitura bloqueiam tudo.

 3. Lock (ReentrantLock)
➤ Comportamento observado:

Executa exatamente como Synk, porém:

Em algumas execuções, uma segunda thread (Filho 2) consegue sacar.

O saldo nunca fica incorreto.

A lógica funciona sem corrupção de dados.

➤ Causa:

Lock permite um controle mais refinado, com lock explícito na operação crítica.

➤ Conclusão:

✔ Seguro
✔ Melhor controle do que synchronized
✔ Permite múltiplos saques quando há mais de uma conta envolvida
✖ Requer cuidado com lock() e unlock().

 4. RwLock (ReentrantReadWriteLock)
➤ Comportamento observado:

O Filho quase sempre consegue sacar primeiro.

Pai e Mãe ficam com saldo insuficiente.

O saldo nunca fica incorreto, mas apenas 1 saque ocorre.

➤ Causa:

ReadWriteLock permite:

várias threads lendo

apenas 1 thread escrevendo

Mas neste caso saque = escrita, então só uma thread modifica o saldo.

➤ Conclusão:

✔ Seguro
✔ Bom quando há muitas leituras e poucas escritas
✖ Aqui não traz vantagem real, pois todos estão tentando sacar.

🧠 Conclusão Geral do Projeto
Implementação	Segurança do saldo	Permite múltiplos saques?	Problemas
Concorrente	❌ Não	❌ Não	Corrida, saldo inconsistente
Synk	✔ Sim	❌ Não (sequencial)	Bloqueio total
Lock	✔ Sim	✔ Às vezes	Mais verboso
RwLock	✔ Sim	❌ Não neste caso	Não aproveita múltiplos leitores
