# Aquecedor de cache do maremo.to

Mantém o cache de páginas do LiteSpeed sempre quente, para que nenhum visitante
seja o primeiro a montar uma página do zero.

## O problema que ele resolve

Servir uma página já em cache é instantâneo. Montá-la do zero é caro - e quem paga
esse custo é justamente quem chega primeiro depois de o cache ser esvaziado.

O cache esvazia sozinho em duas situações que não dependem de ninguém lembrar:

- quando o TTL de 7 dias expira;
- quando um plugin, o tema ou o próprio WordPress é atualizado.

Este workflow garante que, nessas horas, quem monta as páginas seja um robô.

## Como funciona

Duas vezes por dia, lê o `sitemap_index.xml` do site, extrai todas as URLs e visita
uma a uma. Páginas novas entram sozinhas - não existe lista fixa para manter.

O resumo de cada execução informa quantas páginas estavam frias e quais eram.

## Depois de mexer no site

Editar qualquer coisa no WordPress limpa o cache inteiro. Para reaquecer na hora,
sem esperar a próxima execução agendada:

**Actions → Aquecer cache do maremo.to → Run workflow**

## Detalhe importante

O cabeçalho `Accept` enviado pelo script anuncia suporte a WebP de propósito.
O LiteSpeed mantém variantes de cache separadas para navegadores que aceitam WebP
e para os que não aceitam. Um aquecedor sem esse cabeçalho esquentaria a variante
que quase ninguém usa, e os visitantes reais continuariam encontrando páginas frias
- com a falsa impressão de que o aquecimento está funcionando.

## Quando o servidor recusa a conexao

O firewall da hospedagem derruba conexoes vindas do GitHub de vez em quando, sem
padrao: numa mesma execucao, uma requisicao trava e a seguinte passa. Foi isso que
quebrou o aquecedor em 22/08/2026 - o script morria na primeira recusa e nao dizia
por que.

Agora cada requisicao tem cinco tentativas com intervalo, e ha um segundo de pausa
entre uma pagina e outra para a visita nao parecer uma varredura. Falha isolada de
pagina vira aviso amarelo; a execucao so fica vermelha se o sitemap nao puder ser
lido ou se mais de um terco do site recusar conexao. Nesses casos o resumo diz
exatamente qual foi o erro.

Se a execucao ficar vermelha de novo por recusa de conexao, o assunto e com a
E-consulters: o bloqueio esta no servidor, nao no script.
