Landing page de captação de revendedores da Oliar Cosméticos. Apresenta a marca, o convite de parceria e um formulário de interesse que envia os dados por e-mail para a equipe comercial.

Site publicado: https://revenda.oliarcosmeticos.com
Site institucional: https://oliarcosmeticos.com
Sumário
Visão geral
Tecnologias
Estrutura do projeto
Como rodar localmente
Como funciona o formulário
Configurando o envio de e-mail (FormSubmit)
Deploy na Vercel
Personalização
Boas práticas e segurança
Direitos autorais
Visão geral

A página é composta por duas áreas lado a lado (empilhadas no celular):

Área visual: foto do produto (Cleanser Oliar 160 ml), título "Beleza que cresce junto." e texto de apresentação.
Formulário de cadastro: captura os dados de quem deseja revender a marca.

Campos do formulário:

Campo	Tipo	Obrigatório
Nome completo	Texto (3 a 120 caracteres)	Sim
E-mail	E-mail (até 254 caracteres)	Sim
Tipo de cadastro	Pessoa Física (PF) ou Pessoa Jurídica (PJ)	Sim
Interesse em revender a Oliar	Texto longo (10 a 3000 caracteres)	Sim
Tecnologias

Projeto 100% estático, sem build, sem dependências e sem framework:

HTML5, CSS3 e JavaScript puro (vanilla)
Fontes DM Sans e Manrope via Google Fonts
FormSubmit para o envio do formulário por e-mail
Hospedagem na Vercel
Estrutura do projeto
.
├── index.html   # Página completa (HTML + CSS + JS + imagem do produto embutida)
└── README.md

Tudo está em um único arquivo. A foto do produto está embutida no próprio index.html (base64), então não há pasta de imagens para subir junto.

Como rodar localmente

Não é preciso instalar nada. Para visualizar o layout, basta abrir o index.html no navegador.

Para servir em um servidor local (opcional):

bash
# Com Python
python3 -m http.server 8000

# Ou com Node.js
npx serve .

Depois acesse http://localhost:8000.

Atenção: o envio do formulário não funciona abrindo o arquivo diretamente do computador (file://), pois o FormSubmit bloqueia esse tipo de origem. A página exibe um aviso nesse caso. Para testar o envio, use o site publicado ou um servidor local (http://localhost).

Como funciona o formulário

O envio é feito via fetch (AJAX) para o FormSubmit, sem recarregar a página. O fluxo é:

Validação nativa do navegador (reportValidity).
Verificação do campo honeypot (anti-robô).
Envio dos dados com FormData para FORM_ENDPOINT, com tempo limite de 15 segundos.
Mensagem de sucesso ou erro exibida abaixo do botão, com acessibilidade para leitores de tela (aria-live).

Proteções e comportamentos incluídos:

Honeypot: campo oculto _honey. Se preenchido (robô), a página finge sucesso e não envia nada.
Tempo mínimo de preenchimento: se o envio ocorrer rápido demais (menos de 2,5 s após abrir a página), o script aguarda o restante em vez de falhar.
Botão bloqueado durante o envio, evitando cadastros duplicados por cliques repetidos.
Tratamento de erros: mensagens específicas para falta de ativação, tempo esgotado e falha de conexão.

Configurações enviadas ao FormSubmit (campos ocultos no HTML):

Campo	Valor	Função
_subject	Novo interesse em revenda — Oliar Cosméticos	Assunto do e-mail recebido
_template	table	Formato do e-mail em tabela
_captcha	false	Desativa o captcha do FormSubmit
Configurando o envio de e-mail (FormSubmit)

A configuração fica no topo do <script> do index.html:

js
const FORM_ENDPOINT = 'https://formsubmit.co/ajax/SEU_EMAIL_OU_CODIGO';
Primeira ativação
Publique a página (Vercel ou outro servidor).
Envie um formulário de teste pelo site publicado.
O FormSubmit enviará um e-mail de ativação para o endereço configurado (verifique também o spam).
Clique no link de confirmação. A partir daí os cadastros passam a chegar por e-mail.
Esconder o e-mail do código (recomendado)

Depois de ativar, o FormSubmit envia um código aleatório de endpoint. Troque o e-mail por esse código:

js
const FORM_ENDPOINT = 'https://formsubmit.co/ajax/a1b2c3d4e5f6...';

Assim, o endereço de e-mail não fica visível no código-fonte nem no repositório do GitHub, o que reduz o risco de spam e de uso indevido.

Trocar o e-mail de destino

Altere o valor de FORM_ENDPOINT e repita o processo de ativação para o novo endereço.

Deploy na Vercel

Por ser um site estático, não exige configuração especial.

Via GitHub (recomendado):

Suba este repositório no GitHub.
Na Vercel, clique em Add New → Project e importe o repositório.
Em Framework Preset, deixe Other. Não precisa de Build Command nem de Output Directory.
Clique em Deploy. A cada git push na branch principal, a Vercel publica automaticamente.

Domínio próprio: em Settings → Domains no projeto da Vercel, adicione o domínio desejado e siga as instruções de DNS.

Personalização
Cores

As cores principais estão em variáveis CSS no início do <style>:

css
:root {
  --cream: #f7f4ee;
  --paper: #fffcf7;
  --ink:   #302c27;
  --muted: #777067;
  --sand:  #ded2bd;
  --brown: #806c56;
  --line:  #e7dfd3;
}
Textos

Títulos, chamadas e textos legais estão diretamente no HTML. Procure por Beleza que cresce junto., Seja um revendedor Oliar. e pelo texto de consentimento (classe .fine).

Imagem do produto

A imagem está na tag <img class="product">, dentro da seção .visual, em formato base64. Para trocar:

Mantendo tudo em um arquivo: converta a nova imagem para base64 (de preferência JPEG, até ~900 px de largura e abaixo de 100 KB) e substitua o conteúdo de src.
Usando arquivo separado: salve a imagem na raiz do projeto (por exemplo produto.jpg) e use src="produto.jpg".

A imagem usa mix-blend-mode: multiply, que funciona melhor com fundo branco, pois ele se mistura com o degradê da página. Para imagens com fundo transparente (PNG/WebP), esse efeito não é necessário.

Boas práticas e segurança
Não publique chaves, senhas ou dados pessoais no repositório. O FormSubmit não exige chave de API, mas o e-mail de destino fica visível no código até ser trocado pelo código aleatório (veja acima).
Os dados enviados pelos interessados (nome, e-mail e texto livre) são dados pessoais. Trate-os conforme a LGPD (Lei nº 13.709/2018) e mantenha o aviso de consentimento do formulário.
Considere publicar uma Política de Privacidade e linkar no texto de consentimento.
Como o repositório é de um projeto comercial, avalie mantê-lo privado no GitHub.
Direitos autorais

© 2026 Oliar Cosméticos. Todos os direitos reservados.

O código, o texto, a marca, o logotipo e as imagens deste repositório são de propriedade da Oliar Cosméticos e não podem ser copiados, redistribuídos ou utilizados sem autorização prévia.
