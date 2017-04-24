# Consulta de Cadastro
Consulta Cadastro de Contribuinte do ICMS de uma UF.

**Função:** Serviço para consultar o cadastro de contribuintes do ICMS da unidade federada.

**Processo:** síncrono. A solicitação e o retorno ocorrem em uma única fase.

**Método:** consultaCadastro

## Descrição
Permite consultar a situação cadastral de um contribuinte em uma determinada UF, a funcionalidade é util para verificar se o destinatário da NF-e é um contribuinte em situação regular na UF de destino. Lembrando que quando o destinatário está inabilitado a NFe será DENEGADA.

> NOTA: Funciona somente para modelo 55 (NFe), o modelo 65 (NFCe) evidentemente não possue esse tipo de serviço.

**Escopo:** A consulta equivale à consulta SINTEGRA, isto é consulta a situação de destinatário que sejam inscritos no cadastro de contribuintes do ICMS na UF consultada.

**Disponibilidade:** Nem todas as UF oferecem a consulta, como regra geral podemos dizer que somente as UF que tem sistemas próprios de recepção de NF-e oferecem a consulta. Para saber quais SEFAZ oferecem esse serviço consulte sua SEFAZ. Caso a UF pesquisada não ofereça o serviço será retornada uma EXCEPTION.

**Informações:** Existe previsão para retorno dos dados cadastrais, mas nem todas as UF retornam todas as informações. A informação que todas UF, que possuem o serviço retornam é o CNPJ/CPF, Inscrição Estadual, Razão Social e situação cadastral: 0 - não habilitado ou 1 - habilitado

## Exemplo de Uso

```php

use NFePHP\NFe\Tools;
use NFePHP\Common\Certificate;
use NFePHP\NFe\Common\Standardize;

try {

    $certificate = Certificate::readPfx($content, 'senha');
    $tools = new Tools($configJson, $certificate);
    $tools->model('55');

    $uf = 'SP';
    $cnpj = '00822602000111';
    $iest = '';
    $cpf = '';
    $response = $tools->sefazCadastro($uf, $cnpj, $iest, $cpf);

    //você pode padronizar os dados de retorno atraves da classe abaixo
    //de forma a facilitar a extração dos dados do XML
    //NOTA: mas lembre-se que esse XML muitas vezes será necessário, 
    //      quando houver a necessidade de protocolos
    $stdCl = new Standardize($response);
    //nesse caso $std irá conter uma representação em stdClass do XML
    $std = $stdCl->toStd();
    //nesse caso o $arr irá conter uma representação em array do XML
    $arr = $stdCl->toArray();
    //nesse caso o $json irá conter uma representação em JSON do XML
    $json = $stdCl->toJson();

} catch (\Exception $e) {
    echo $e-<getMessage();
}

```
> NOTA: Os campos não serão verificados quanto a validade de seu conteúdo, antes do envio, **isso não é função da API**.

> NOTA: A prioridade é na ordem CNPJ, IEST e CPF, mesmo que você indique os três apenas o CNPJ será usado. 


## Parametros

| Variável      | Detalhamento  |
| ------------- | ------------- |
| $configJson   | String Json com os dados de configuração(OBRIGATÓRIO)  |
| $content      | String com o centeúdo do certificado PFX |
| $certificado  | Classe Certificate::class contendo o certificado digital(OBRIGATÓRIO)  |
| $uf           | Sigla da unidade da Federação a quem pertence o documento pesquisado (OBRIGATÓRIO) |
| $cnpj         | Número do CNPJ *sem formatação* (OPCIONAL) |
| $iest         | Número da Inscrição estadual *sem formatação* (OPCIONAL) |
| $cpf          | Número do Cadastro de Pessoa Física *sem formatação* (OPCIONAL) |

## Mensagens


### ENVIO

```xml
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:soap="http://www.w3.org/2003/05/soap-envelope">
  <soap:Header>
    <nfeCabecMsg xmlns="http://www.portalfiscal.inf.br/nfe/wsdl/CadConsultaCadastro2">
      <cUF>35</cUF>
      <versaoDados>2.00</versaoDados>
    </nfeCabecMsg>
  </soap:Header>
  <soap:Body>
    <nfeDadosMsg xmlns="http://www.portalfiscal.inf.br/nfe/wsdl/CadConsultaCadastro2">
      <ConsCad xmlns="http://www.portalfiscal.inf.br/nfe" versao="2.00">
        <infCons>
          <xServ>CONS-CAD</xServ>
          <UF>SP</UF>
          <CNPJ>00822602000111</CNPJ>
        </infCons>
      </ConsCad>
    </nfeDadosMsg>
  </soap:Body>
</soap:Envelope>
```

### RETORNO

```xml
<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
    <soap:Header>
        <nfeCabecMsg xmlns="http://www.portalfiscal.inf.br/nfe/wsdl/CadConsultaCadastro2">
            <cUF>35</cUF>
            <versaoDados>2.00</versaoDados>
        </nfeCabecMsg>
    </soap:Header>
    <soap:Body>
        <consultaCadastro2Result xmlns="http://www.portalfiscal.inf.br/nfe/wsdl/CadConsultaCadastro2">
            <retConsCad versao="2.00" xmlns="http://www.portalfiscal.inf.br/nfe">
                <infCons>
                    <verAplic>SP_NFE_PL_008i2</verAplic>
                    <cStat>111</cStat>
                    <xMotivo>Consulta cadastro com uma ocorrência</xMotivo>
                    <UF>SP</UF>
                    <CNPJ>00822602000111</CNPJ>
                    <dhCons>2017-04-24T17:12:13-03:00</dhCons>
                    <cUF>35</cUF>
                    <infCad>
                        <IE>888889114119</IE>
                        <CNPJ>00822602000111</CNPJ>
                        <UF>SP</UF>
                        <cSit>1</cSit>
                        <indCredNFe>1</indCredNFe>
                        <indCredCTe>4</indCredCTe>
                        <xNome>FAKE SISTEMAS E EQUIPAMENTOS LTDA - ME</xNome>
                        <xRegApur>NORMAL - REGIME PERIÓDICO DE APURAÇÃO</xRegApur>
                        <CNAE>4651601</CNAE>
                        <dIniAtiv>1995-11-06</dIniAtiv>
                        <dUltSit>1995-11-06</dUltSit>
                        <ender>
                            <xLgr>RUA GUIDO ALIBERTI</xLgr>
                            <nro>5453</nro>
                            <xBairro>SAO JOSE</xBairro>
                            <cMun>3548807</cMun>
                            <xMun>SAO CAETANO DO SUL</xMun>
                            <CEP>09580400</CEP>
                        </ender>
                    </infCad>
                </infCons>
            </retConsCad>
        </consultaCadastro2Result>
    </soap:Body>
</soap:Envelope>

```

## Standardize

Esses retornos em XML podem ser "padronizados" para facilitar a extração de dados.

Vide classe [Standardize](Standardize)

### ARRAY 

```
Array
(
    [attributes] => Array
        (
            [versao] => 2.00
        )

    [infCons] => Array
        (
            [verAplic] => SP_NFE_PL_008i2
            [cStat] => 111
            [xMotivo] => Consulta cadastro com uma ocorrência
            [UF] => SP
            [CNPJ] => 00822602000111
            [dhCons] => 2017-04-24T17:12:13-03:00
            [cUF] => 35
            [infCad] => Array
                (
                    [IE] => 888889114119
                    [CNPJ] => 00822602000111
                    [UF] => SP
                    [cSit] => 1
                    [indCredNFe] => 1
                    [indCredCTe] => 4
                    [xNome] => FAKE SISTEMAS E EQUIPAMENTOS LTDA - ME
                    [xRegApur] => NORMAL - REGIME PERIÓDICO DE APURAÇÃO
                    [CNAE] => 4651601
                    [dIniAtiv] => 1995-11-06
                    [dUltSit] => 1995-11-06
                    [ender] => Array
                        (
                            [xLgr] => RUA GUIDO ALIBERTI
                            [nro] => 5453
                            [xBairro] => SAO JOSE
                            [cMun] => 3548807
                            [xMun] => SAO CAETANO DO SUL
                            [CEP] => 09580400
                        )

                )

        )

)
```

### JSON STRING

```json
{
    "attributes": {
        "versao": "2.00"
    },
    "infCons": {
        "verAplic": "SP_NFE_PL_008i2",
        "cStat": "111",
        "xMotivo": "Consulta cadastro com uma ocorr\u00eancia",
        "UF": "SP",
        "CNPJ": "00822602000111",
        "dhCons": "2017-04-24T17:12:13-03:00",
        "cUF": "35",
        "infCad": {
            "IE": "888889114119",
            "CNPJ": "00822602000111",
            "UF": "SP",
            "cSit": "1",
            "indCredNFe": "1",
            "indCredCTe": "4",
            "xNome": "FAKE SISTEMAS E EQUIPAMENTOS LTDA - ME",
            "xRegApur": "NORMAL - REGIME PERI\u00d3DICO DE APURA\u00c7\u00c3O",
            "CNAE": "4651601",
            "dIniAtiv": "1995-11-06",
            "dUltSit": "1995-11-06",
            "ender": {
                "xLgr": "RUA GUIDO ALIBERTI",
                "nro": "5453",
                "xBairro": "SAO JOSE",
                "cMun": "3548807",
                "xMun": "SAO CAETANO DO SUL",
                "CEP": "09580400"
            }
        }
    }
}
```

### STDCLASS

```
stdClass Object
(
    [attributes] => stdClass Object
        (
            [versao] => 2.00
        )

    [infCons] => stdClass Object
        (
            [verAplic] => SP_NFE_PL_008i2
            [cStat] => 111
            [xMotivo] => Consulta cadastro com uma ocorrência
            [UF] => SP
            [CNPJ] => 00822602000111
            [dhCons] => 2017-04-24T17:12:13-03:00
            [cUF] => 35
            [infCad] => stdClass Object
                (
                    [IE] => 888889114119
                    [CNPJ] => 00822602000111
                    [UF] => SP
                    [cSit] => 1
                    [indCredNFe] => 1
                    [indCredCTe] => 4
                    [xNome] => FAKE SISTEMAS E EQUIPAMENTOS LTDA - ME
                    [xRegApur] => NORMAL - REGIME PERIÓDICO DE APURAÇÃO
                    [CNAE] => 4651601
                    [dIniAtiv] => 1995-11-06
                    [dUltSit] => 1995-11-06
                    [ender] => stdClass Object
                        (
                            [xLgr] => RUA GUIDO ALIBERTI
                            [nro] => 5453
                            [xBairro] => SAO JOSE
                            [cMun] => 3548807
                            [xMun] => SAO CAETANO DO SUL
                            [CEP] => 09580400
                        )

                )

        )

)
```


## Mensagens de ERRO (Exceptions)

Caso não passe em alguma validação ou sejam encontrados problemas na comunicação, será SEMPRE retornado um EXCEPTION que deve ser capturado.

Mas os erros não se restringem a esse tipo de falha. Além de falhas na fase de montagem da mensagem e na comunicação podem ser retornados erros relativos a analise pelas regras de negócios da SEFAZ nesse caso os erros deverão ser analisados no xml de retorno.
