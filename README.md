# web-scraping-books
Raspagem de dados 


!pip install requests beautifulsoup4

import requests

from bs4 import BeautifulSoup

import csv

# Passo 3: Defina o URL da página que você quer baixar
url = 'https://books.toscrape.com/index.html'

# Passo 4: Faça a requisição HTTP para a página
# requests.get() envia uma solicitação e retorna um objeto de resposta
try:
    response = requests.get(url)
    # response.raise_for_status() irá gerar um erro se a requisição falhar
    response.raise_for_status()

    # Se a requisição foi bem-sucedida, o conteúdo HTML está em response.text

    # Passo 5: Use Beautiful Soup para analisar o conteúdo HTML
    # BeautifulSoup transforma o HTML em um objeto navegável e pesquisável
    soup = BeautifulSoup(response.text, 'html.parser')

    # Passo 6: Encontre e extraia uma informação específica da página
    # Neste caso, vamos encontrar a tag <title>
    titulo = soup.title.string

    precos = soup.find_all('p', class_='price_color')

    # Passo 7: Itere sobre a lista de preços e imprima cada um
    print("Preços encontrados na página:")
    for preco in precos:
        # O .text extrai apenas o texto de dentro da tag, removendo o HTML
        print(preco.text)

         # Passo 4: Prepare o arquivo CSV para salvar os dados
    # A linha abaixo cria e abre o arquivo no modo de escrita ('w')
    # O argumento newline='' evita linhas em branco extras no arquivo CSV
    with open('precos_livros.csv', 'w', newline='', encoding='utf-8') as arquivo_csv:
        # Crie um objeto "writer" para escrever no arquivo
        writer = csv.writer(arquivo_csv)

        # Escreva a linha de cabeçalho do arquivo
        writer.writerow(['Preço'])

        # Passo 5: Itere sobre a lista de preços e salve no arquivo
        for preco in precos:
            # O .text extrai o texto da tag. Vamos remover o símbolo de libra '£'
            texto_limpo = preco.text.replace('£', '')

            # Escreva o preço como uma nova linha no arquivo CSV
            writer.writerow([texto_limpo])

    print("Dados salvos com sucesso no arquivo 'precos_livros.csv'!")

except requests.exceptions.RequestException as e:
    print(f"Ocorreu um erro ao tentar acessar a página: {e}")

    # Passo 7: Imprima a informação extraída
    print(f"O conteúdo do HTML da página foi baixado com sucesso. O título da página é:\n'{titulo}'")

except requests.exceptions.RequestException as e:
    # Se houver um erro na requisição (ex: página não encontrada, sem internet)
    print(f"Ocorreu um erro ao tentar acessar a página: {e}")
