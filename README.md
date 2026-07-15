# old-browsers-behaviors

// Esse bloco captura o erro do Angular e garante que ele apareça com destaque nos logs
Cypress.on('uncaught:exception', (err, runnable) => {
  // Cria um log visual vermelho no painel esquerdo do Cypress
  Cypress.log({
    name: '🔴 ERRO DETECTADO',
    message: err.message,
    consoleProps: () => ({ 'Stack Trace': err.stack })
  });
  
  // Imprime o erro detalhado no Console (F12) para o seu acompanhamento
  console.error('%c[Navegador Simulado Estourou Erro]:', 'background: red; color: white; padding: 5px;', err.stack);
  
  // RETORNAR FALSE É CRUCIAL: impede o Cypress de fechar o browser ou parar o teste.
  // Isso mantém o erro estourado na tela e visível para o seu log/print.
  return false; 
});

// 2. OS TESTES DE SIMULAÇÃO (Ficam logo abaixo na mesma spec)
describe('Testes de Emulação de Browsers Antigos', () => {

  const URL_APLICACAO = 'http://localhost:4200'; // Altere para a URL do seu projeto
  const AGENT_IE11 = 'Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; rv:11.0) like Gecko';
  const AGENT_SAFARI_7 = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_3) AppleWebKit/537.75.14 Version/7.0.3 Safari/537.75.14';

  it('Cenário 1: Simular Internet Explorer 11', () => {
    cy.log('==================================================');
    cy.log('🌐 INICIANDO AMBIENTE: Internet Explorer 11');
    cy.log(`🕵️ User Agent: ${AGENT_IE11}`);
    cy.log('==================================================');

    cy.visit(URL_APLICACAO, {
      onBeforeLoad(win) {
        // Altera o User Agent para o do IE11
        Object.defineProperty(win.navigator, 'userAgent', { get: () => AGENT_IE11 });
        
        // Remove a propriedade 'languages' (IE11 não tem essa propriedade)
        Object.defineProperty(win.navigator, 'languages', { get: () => undefined });
      }
    });

    // Esse comando serve apenas para dar tempo do Angular carregar e o erro estourar na tela
    cy.wait(3000); 
  });

  it('Cenário 2: Simular Safari Antigo (v7.0)', () => {
    cy.log('==================================================');
    cy.log('🌐 INICIANDO AMBIENTE: Safari Antigo (v7.0)');
    cy.log(`🕵️ User Agent: ${AGENT_SAFARI_7}`);
    cy.log('==================================================');

    cy.visit(URL_APLICACAO, {
      onBeforeLoad(win) {
        // Altera o User Agent para o do Safari antigo
        Object.defineProperty(win.navigator, 'userAgent', { get: () => AGENT_SAFARI_7 });

        // Faz o 'languages' existir, mas retorna um Objeto comum em vez de Array (não tem .forEach)
        Object.defineProperty(win.navigator, 'languages', {
          get: () => ({ 0: 'pt-BR', length: 1 })
        });
      }
    });

    cy.wait(3000);
  });

});

