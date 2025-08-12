# docxtemplater-esm-bundle

Projeto que converteu a biblioteca [docxtemplater](https://docxtemplater.com/) para um bundle ESM utilizando `esbuild --format=esm --bundle`.  
Ideal para uso direto em navegadores ou via CDN (exemplo com [jsdelivr](https://www.jsdelivr.com/)) dessa forma:

```js
import { generateDocument } from 'https://cdn.jsdelivr.net/gh/brunodavi/docxtemplater-esm-dist@main/dist/docxtemplater.bundle.mjs'
```

## Como Usar

```js
import PizZip from 'pizzip';
import Docxtemplater from 'docxtemplater';

/**
 * Gera um documento .docx a partir de um template e dados, com opções configuráveis.
 * * @param {ArrayBuffer | string} content - O conteúdo do arquivo de template .docx.
 * @param {object} data - O objeto com os dados para preencher o template.
 * @param {object} [docxtemplaterOptions={}] - Opções para o construtor do Docxtemplater.
 * @property {boolean} [paragraphLoop=true] - Habilita loops em parágrafos.
 * @property {boolean} [linebreaks=true] - Converte \n e \r\n para quebras de linha no Word.
 * @param {object} [outputOptions={}] - Opções para a geração do arquivo de saída pelo PizZip.
 * @property {'base64' | 'uint8array' | 'string' | 'nodebuffer' | 'blob'} [type='base64'] - O formato do arquivo de saída.
 * @property {'DEFLATE' | 'STORE'} [compression='DEFLATE'] - O tipo de compressão.
 * * @returns {string | Uint8Array | object} - O documento gerado, no formato especificado em `outputOptions.type`.
 */
export function generateDocument(
    content,
    data,
    docxtemplaterOptions = {},
    outputOptions = {}
) {
    try {
        // 1. Define as opções padrão e as mescla com as fornecidas pelo usuário.
        // As opções do usuário sobrescrevem as padrão.
        const finalDocxOptions = {
            paragraphLoop: true,
            linebreaks: true,
            ...docxtemplaterOptions, // Mescla as opções do Docxtemplater
        };

        const finalOutputOptions = {
            type: 'base64',
            compression: 'DEFLATE',
            ...outputOptions, // Mescla as opções de saída
        };

        // 2. Cria o documento com as opções finais
        const zip = new PizZip(content);
        const doc = new Docxtemplater(zip, finalDocxOptions);

        // 3. Renderiza os dados
        doc.render(data);

        // 4. Gera a saída com as opções finais
        const output = doc.getZip().generate(finalOutputOptions);

        return output;

    } catch (error) {
        error.message = `Docxtemplater error: ${error.message}`;
        throw error;
    }
}
```
