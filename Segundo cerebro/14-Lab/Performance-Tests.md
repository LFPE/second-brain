# Lab: Testes de Carga & Benchmark de APIs 🧪

Rascunhos e scripts locais para simular concorrência e medir tempos de resposta das rotas.

---

## Script Rápido de Benchmark (Node.js)
Código Javascript local para disparar requisições concorrentes e calcular média de tempo de resposta da API:

```javascript
const url = 'https://jurishub-api.render.com/api/clientes';
const requestsCount = 100;

async function runBenchmark() {
  const start = Date.now();
  const promises = Array.from({ length: requestsCount }).map(async () => {
    try {
      const res = await fetch(url);
      return res.status;
    } catch {
      return 500;
    }
  });

  const results = await Promise.all(promises);
  const duration = Date.now() - start;
  const successCount = results.filter(s => s === 200).length;

  console.log(`--- BENCHMARK RESULTS ---`);
  console.log(`Requests: ${requestsCount}`);
  console.log(`Success: ${successCount}`);
  console.log(`Duration: ${duration}ms`);
  console.log(`Average: ${(duration / requestsCount).toFixed(2)}ms/req`);
}

runBenchmark();
```
