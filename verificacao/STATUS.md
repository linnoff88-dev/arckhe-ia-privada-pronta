# Validação real em Linux — 15/11/2026

-14testes de software passaram, incluindo iniciar/responder/encerrar uma instância simulada.
- Modelo real: `llama3.2:latest`, com digest conferido.
- Inferência real dentro de rede isolada contendo somente `lo`.
- Pergunta17+25: respondeu42.
- Contexto em duas mensagens: respondeuLARANJA corretamente.
- Documento fictício: resposta `600`; gabarito1000; errou.
- Modelo descarregado e processo próprio encerrado depois dos testes; porta fechada.
- A inferência foi autorizada expressamente pelo Nei, feita um modelo por vez sob fila de GPU.
- Não houve teste em Windows/macOS nem implantação em nuvem.

Evidência: [prova-real-linux.json](prova-real-linux.json).

O Llama3.2 também errou esse exercício na tentativa anterior, respondendo800; na tentativa registrada no JSON respondeu600. O gabarito1000foi preservado. Isso limita o uso do modelo leve para contas em documentos, mas não altera a prova de execução local/sem rede.
