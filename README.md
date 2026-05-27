import { Octokit } from "@octokit/rest";

// 1. Configure suas credenciais e o repositório
const octokit = new Octokit({
  auth: "SEU_GITHUB_PERSONAL_ACCESS_TOKEN" // Substitua pelo seu token
});

const REPO_OWNER = "SEU_USUARIO_DO_GITHUB";
const REPO_NAME = "NOME_DO_SEU_REPOSITORIO";
const BRANCH = "main"; // ou a branch que você estiver usando

/**
 * Função para salvar um arquivo (texto ou imagem) no GitHub
 * @param {string} pathInRepo - O caminho completo e nome do arquivo dentro do repositório (ex: 'conteudo/post1.txt')
 * @param {string|Buffer} content - O conteúdo do arquivo
 * @param {string} commitMessage - Mensagem do commit
 * @param {boolean} isBinary - Define se o arquivo é binário (como imagens)
 */
async function salvarNoGithub(pathInRepo, content, commitMessage, isBinary = false) {
  try {
    // Converte o conteúdo para Base64 (exigido pela API do GitHub)
    const contentBase64 = isBinary 
      ? content.toString("base64") 
      : Buffer.from(content).toString("base64");

    // Envia o arquivo para o repositório
    await octokit.repos.createOrUpdateFileContents({
      owner: REPO_OWNER,
      repo: REPO_NAME,
      path: pathInRepo,
      message: commitMessage,
      content: contentBase64,
      branch: BRANCH
    });

    console.log(`✅ Sucesso: ${pathInRepo} foi salvo no GitHub!`);
  } catch (error) {
    console.error(`❌ Erro ao salvar ${pathInRepo}:`, error.message);
  }
}

// 2. Execução Principal
async function principal() {
  console.log("Iniciando o upload dos conteúdos baseados nas fontes...");

  // Exemplo de texto gerado para as redes sociais/blog
  const textoPost = `
  🌱 GOVERNO FORTALECE COMBATE A INCÊNDIOS FLORESTAIS EM 2026

  O Ministério do Meio Ambiente e Mudança do Clima (MMA) realizou a primeira reunião do ano da Sala de Situação sobre Incêndios Florestais. O foco é antecipar o planejamento e coordenar ações preventivas contra o fogo em biomas críticos.
  
  Essas ações dão continuidade ao histórico de normativas de gestão ambiental, como as diretrizes de manejo integradas pelo ICMBio ao longo dos anos.
  
  #MeioAmbiente #Sustentabilidade #Prevfogo #Brasil
  `.trim();

  // Exemplo de uma imagem bufferizada (Simulação de um download de imagem)
  // Na prática, você usaria o 'fs.readFileSync("caminho/da/imagem.png")' ou um fetch da imagem.
  const imagemBuffer = Buffer.from("iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVR42mNk+M9QDwADhgGAWjR9awAAAABJRU5ErkJggg==", "base64"); // Um pixel transparente de exemplo

  // Definindo a pasta de destino no repositório (ex: 'conteudo/posts_2026/')
  const pastaDestino = "conteudo/posts_2026";

  // Salvar o texto (Artigo/Post)
  await salvarNoGithub(
    `${pastaDestino}/post-incendios.txt`,
    textoPost,
    "feat: adiciona texto do post sobre incêndios florestais"
  );

  // Salvar a imagem correspondente
  await salvarNoGithub(
    `${pastaDestino}/imagem-incendios.png`,
    imagemBuffer,
    "feat: adiciona imagem do post sobre incêndios florestais",
    true // Define como binário
  );
}

principal();
