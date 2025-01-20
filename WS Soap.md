# SOAP
O SOAP (Simple Object Access Protocol), no contexto de APIs (Interfaces de Programação de Aplicações), é um protocolo de comunicação baseado em XML utilizado para troca de dados entre sistemas distribuídos, geralmente em aplicações web. Ao contrário de REST, que é mais flexível e popular em APIs modernas, o SOAP é mais rigoroso, com um conjunto de regras e padrões bem definidos.
O SOAP define uma estrutura de mensagens que inclui uma cabeçalho e um corpo (body), e é geralmente usado com protocolos de transporte como HTTP ou SMTP.

## Índice
- [Uso do SOAP no ISS](#uso-do-soap-no-iss)
- [SOAP Estrutura](#soap-estrutura)
- [Links](#links)

## Uso do SOAP no ISS
Atualmente é possível enviar requisições do tipo POST para o endpoint **/iss-ws/nfseService**, utilizando o SOAPUI, Postman e um aplicativo interno com nome de SITS, para realizar operações de cadastro e edição de notas fiscais.
Ao utilizar o SITS, é informado um arquivo XML que posteriormente é encapsulado e transformado em arquivo SOAP, o qual será apresentado um exemplo no tópico **POST SOAP com XML**.
Para pegar uma solicitação SOAP pronta, utilize o seguinte SQL e copie a solicitação da coluna solicitacao (atente-se ao tiposolicitacao).
```sql
select * from iss.isssolicitacaows i
```

1. **SOAP UI**: Ao utilizar a ferramenta do **SOAP UI**, se faz necessário enviar uma requisição envelopada no padrão soap, que será explicado no tópico [SOAP Estrutura](#soap-estrutura) e poderá ser utilizado a requisição do sql acima.

- Baixe o [SOAP UI](https://www.soapui.org/downloads/soapui/)
- Baixe o arquivo [pré-configurado](https://drive.google.com/drive/folders/1SVV9MgWIKsTDLMfcIYBXDeAxFQ-TNzI4?usp=drive_link) wsiss-soapui-project.xml
- Abra o SOAP UI
- Clique em file e importe o arquivo baixado
- Então será apresentado na pasta projects a pasta wsiss
- Ao expandir as pastas, será possível visualizar a requisição com nome Envio PG Assincrono
- Ao abrir essa requisição, é possível ver o tipo do método, endpoint e resource.
- Em media type, selecione o tipo application/xml e informe o arquivo soap para envio.

2. **Postman**: Ao utilizar a ferramenta do **Postman**, se faz necessário adequar a estrutura, podendo ser realizada de 2 formas:

- Baixe a [coleção postman](https://drive.google.com/drive/folders/1SVV9MgWIKsTDLMfcIYBXDeAxFQ-TNzI4?usp=drive_link) NFSe Endpoint.postman_collection para facilitar o desempenho (opcional)

- **POST SOAP**: Utilizando o envelopamento SOAP-XML, no Postman, sendo o mesmo arquivo que pode ser utilizado no SOAP UI, realizando as configurações em:
```
    **url**
    https://tenant.qa.elotech.com.br/iss-ws/nfseService

    **headers**
    | key          | value                                                          |
    | Accept       | text/xml, text/html, image/gif, image/jpeg, *; q=.2, */*; q=.2 |
    | Content-Type | text/xml;charset=UTF-8                                         |

    **body**
    - raw
    - xml
    - corpo do soap com os dados
```

- **POST SOAP com XML**: Para este caso, é realizado a configuração para se assemelhar ao envio da requisição, estilo ao que já é realizado atualmente pela pasta do SITS.
Neste caso é necessário criar um Environment, seguindo os passos:

```xml
Criar um Environment com qualquer nome (terá uma aba no canto esquerdo com este nome)
Criar uma váriavel com um nome que desejar, por exemplo xml:

| Variable          | Type    | Initial value & Current value                                              |
| xml               | default | pegar um xml e colocar apenas os dados da tag <EnviarLoteRpsSincronoEnvio> |
```
- Essa variável criada, será utilizada no escopo da requisição post a seguir.
- Foi informado a tag EnviarLoteRpsSincronoEnvio, porém pode ser dos outros tipos de post que estão disponíveis no sistema (ConsultarLoteRpsEnvio, CancelarNfseEnvio, ConsultarNfseServicoPrestadoEnvio, ConsultarNfseServicoTomadoEnvio).
- OBS: Lembrar de selecionar o Environment no postman para ter acesso a variável(canto superior esquerdo terá uma opção com o nome No environment).

Configurando a requisição POST:

```xml
    **url**
    https://tenant.qa.elotech.com.br/iss-ws/nfseService

    **headers**
    | key          | value                                                          |
    | Accept       | text/xml, text/html, image/gif, image/jpeg, *; q=.2, */*; q=.2 |
    | Content-Type | text/xml;charset=UTF-8                                         |

    **body**
    - raw
    - xml

<SOAP-ENV:Envelope
	xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/">
	<SOAP-ENV:Header>
		<wsse:Security
			xmlns:wsse="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd"
			xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd" SOAP-ENV:mustUnderstand="1">
			<wsse:BinarySecurityToken EncodingType="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-soap-message-security-1.0#Base64Binary" ValueType="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-x509-token-profile-1.0#X509v3" wsu:Id="X509-9D1EE57EFA6AA8D2CC17346429490071">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</wsse:BinarySecurityToken>
			<ds:Signature
				xmlns:ds="http://www.w3.org/2000/09/xmldsig#" Id="SIG-2">
				<ds:SignedInfo>
					<ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#">
						<ec:InclusiveNamespaces
							xmlns:ec="http://www.w3.org/2001/10/xml-exc-c14n#" PrefixList="SOAP-ENV"/>
						</ds:CanonicalizationMethod>
						<ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1"/>
						<ds:Reference URI="#id-1">
							<ds:Transforms>
								<ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#">
									<ec:InclusiveNamespaces
										xmlns:ec="http://www.w3.org/2001/10/xml-exc-c14n#" PrefixList=""/>
									</ds:Transform>
								</ds:Transforms>
								<ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"/>
								<ds:DigestValue>v5CictAvWqygvSFIn5o7UwiChC0=</ds:DigestValue>
							</ds:Reference>
						</ds:SignedInfo>
						<ds:SignatureValue>dBm2GbeJDr6SpDuLYLa9MgXZg8LBLGeG7uSJ46SmtsO+T0oGIAOE24sE4iCEUwtORkZEQmGgPWmMyyrNghmc6eGQbPBwt6dk6xPS2uZw7Mkmg82eXW1wDNvXiBoBMQaA16+5B9w6hbmL6ZxhtC+ddcXjyVMbt5vwhgvPQJHjJiCc+FLv2esULzUTaKKnLw+01YAmXZUdnqnEd1Xyb/h6x8aDhu/ZLOUQOPOn17udMyzhEwh7tFQsl0jT+2TEMlYcQIBhcswzBn+lA893oPYsDJ5ajxdx0VemNhzv52SZSEN81rQotTBzJn4CUtahwUIA6QWwlKoYdA6FD+5ZfQQu4g==</ds:SignatureValue>
						<ds:KeyInfo Id="KI-9D1EE57EFA6AA8D2CC17346429490092">
							<wsse:SecurityTokenReference wsu:Id="STR-9D1EE57EFA6AA8D2CC17346429490113">
								<wsse:Reference URI="#X509-9D1EE57EFA6AA8D2CC17346429490071" ValueType="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-x509-token-profile-1.0#X509v3"/>
							</wsse:SecurityTokenReference>
						</ds:KeyInfo>
					</ds:Signature>
				</wsse:Security>
			</SOAP-ENV:Header>
			<SOAP-ENV:Body
				xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd" wsu:Id="id-1">
				{{xml}}
			</SOAP-ENV:Body>
		</SOAP-ENV:Envelope>
```

## SOAP Estrutura
**Envelope:** É o contêiner que define a mensagem SOAP e encapsula o cabeçalho e o corpo da mensagem. Ele também define o espaço de nomes (namespace) da mensagem.

```xml
<SOAP-ENV:Envelope
    xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/">
    <SOAP-ENV:Header>
        <!-- informações opcionais como autenticação -->
    </SOAP-ENV:Header>
    <SOAP-ENV:Body>
        <!-- Corpo da mensagem -->
    </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

**Cabeçalho (Header):** O cabeçalho contém informações opcionais como autenticação, transações ou mensagens de controle. Nem todas as mensagens SOAP incluem um cabeçalho.

```xml
<SOAP-ENV:Header>
    <web:AuthToken>123456789</web:AuthToken>
</SOAP-ENV:Header>
```

**Corpo (Body):** O corpo contém os dados reais da mensagem, como a solicitação ou a resposta do serviço. É onde a lógica da aplicação é processada.

```xml
<SOAP-ENV:Body
    xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd" wsu:Id="id-1">
    <EnviarLoteRpsSincronoEnvio
		xmlns="http://shad.elotech.com.br/schemas/iss/nfse_v2_03.xsd">
        <!-- Corpo da mensagem -->
    </EnviarLoteRpsSincronoEnvio>
</SOAP-ENV:Body>
```

## Links
- [Projeto SITS](https://github.com/Elotech-Dev/SITS/tree/master/src/main/java/br/com/elotech/sits)
- [Tutorial para configurar a assinatura no SoapUI](https://www.soapui.org/soapui-projects/ws-security.html)
- [FAQ Webservice de NFSe](https://elogit.elotech.com.br/elotech/oxy/arrecadacao/projetos-iss/-/wikis/FAQ-Webservice-de-NFSe)
- [SOAP UI Download](https://www.soapui.org/downloads/soapui/)
- [Postman Download](https://www.getpostman.com/downloads/)
- [Drive Compartilhado](https://drive.google.com/drive/u/0/folders/1SVV9MgWIKsTDLMfcIYBXDeAxFQ-TNzI4)
- [REST e SOAP: entenda as diferenças](https://www.redhat.com/pt-br/topics/integration/whats-the-difference-between-soap-rest)
- [Qual é a diferença entre o SOAP e o REST?](https://aws.amazon.com/pt/compare/the-difference-between-soap-rest/)
- [SOAP IBM](https://www.ibm.com/docs/pt-br/rsas/7.5.0?topic=standards-soap)