# Boas práticas de arquitetura DDD

Domain-Driven Design (DDD):
O Domain-Driven Design (DDD) é uma abordagem de desenvolvimento de software que coloca o foco principal no domínio do problema que está sendo resolvido. Em DDD, o domínio é o cerne do software, e é nele que a lógica de negócios e as regras estão encapsuladas.
Podemos comparar o domínio com uma "linguagem comum" entre os desenvolvedores e especialistas do domínio. É importante mapear e entender os conceitos do domínio, identificar entidades, agregados, serviços e valores. Dessa forma, podemos criar um modelo de domínio rico e expressivo, que reflete com precisão as necessidades do negócio.

Além disso, DDD incentiva a separação das preocupações em diferentes camadas da aplicação, como a camada de infraestrutura, aplicação e domínio, buscando um design mais modular e desacoplado. Isso facilita a manutenção, testabilidade e evolução do software ao longo do tempo.

```csharp
// Camada de Domínio
namespace MinhaAplicacao.Domain.Models
{
    // Entidades representam objetos do domínio que possuem identidade própria
    public class Cliente
    {
        public int Id { get; set; }
        public string Nome { get; set; }
        public string Email { get; set; }
        // ...
    }

    // Agregados são objetos compostos por entidades e possuem uma raiz de agregado
    public class Pedido
    {
        public int Id { get; set; }
        public Cliente Cliente { get; set; }
        public ICollection<ItemPedido> Itens { get; set; }
        // ...
    }

    // Value Objects representam conceitos imutáveis do domínio e são comparados por valor
    public class Endereco
    {
        public string Rua { get; set; }
        public string Cidade { get; set; }
        public string Estado { get; set; }
        // ...
    }

    // Serviços são responsáveis por operações que não pertencem a uma única entidade ou agregado
    public interface IPedidoService
    {
        void CriarPedido(Pedido pedido);
        void CancelarPedido(int pedidoId);
        // ...
    }

    // Repositórios são interfaces que definem operações de persistência relacionadas a uma entidade
    public interface IClienteRepository
    {
        Cliente ObterPorId(int clienteId);
        void Salvar(Cliente cliente);
        // ...
    }

    // Eventos de domínio representam acontecimentos importantes no sistema
    public class PedidoCriadoEvent
    {
        public Pedido Pedido { get; set; }
        // ...
    }
}

// Camada de Aplicação

namespace MinhaAplicacao.Application.Services
{
    public class PedidoService : IPedidoService
    {
        private readonly IClienteRepository _clienteRepository;

        public PedidoService(IClienteRepository clienteRepository)
        {
            _clienteRepository = clienteRepository;
        }

        public void CriarPedido(Pedido pedido)
        {
            // Lógica de negócio para criar um pedido
            // ...

            // Salvando as alterações no cliente
            var cliente = _clienteRepository.ObterPorId(pedido.Cliente.Id);
            cliente.AtualizarStatusCliente("Ativo");

            // Disparando evento de domínio
            var pedidoCriadoEvent = new PedidoCriadoEvent { Pedido = pedido };
            EventDispatcher.Publish(pedidoCriadoEvent);
        }

        public void CancelarPedido(int pedidoId)
        {
            // Lógica de negócio para cancelar um pedido
            // ...
        }
    }
}

// Camada de Infraestrutura

namespace MinhaAplicacao.Infrastructure.Repositories
{
    public class ClienteRepository : IClienteRepository
    {
        public Cliente ObterPorId(int clienteId)
        {
            // Implementação para obter um cliente do banco de dados
            // ...
        }

        public void Salvar(Cliente cliente)
        {
            // Implementação para salvar um cliente no banco de dados
            // ...
        }
    }
}
```
## Livro
![Imagem](https://m.media-amazon.com/images/I/51YTqGVOD7L._SY425_.jpg)
