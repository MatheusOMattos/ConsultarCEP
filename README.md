# ConsultarCEP
Projeto para encontrar um CEP

﻿using System;
using System.IO;
using System.Net.Http;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        Console.WriteLine(" ======== Bem vindo aos Correios ========");

        Console.Write("Digite o CEP: ");
        string cep = Console.ReadLine();

        // Verificando se o CEP é válido (8 dígitos numéricos)
        if (string.IsNullOrWhiteSpace(cep) || cep.Length != 8 || !long.TryParse(cep, out _))
        {
            Console.WriteLine("CEP inválido. Certifique-se de digitar um CEP com 8 dígitos numéricos.");
        }
        else
        {
            string url = $"https://viacep.com.br/ws/{cep}/json/";

            using (HttpClient client = new HttpClient())
            {
                try
                {
                    var response = await client.GetStringAsync(url);

                    // Verificando se a resposta contém um erro
                    if (response.Contains("\"erro\": true"))
                    {
                        Console.WriteLine("CEP não encontrado.");
                    }
                    else
                    {
                        // Exibindo a resposta no console
                        Console.WriteLine("CEP encontrado com sucesso:");
                        Console.WriteLine(response);

                        string filePath = "cep_info.txt";

                        // Salvando a resposta no arquivo
                        await File.WriteAllTextAsync(filePath, response);
                        Console.WriteLine($"Informações do CEP salvas em {filePath}");
                    }
                }
                catch (HttpRequestException e)
                {
                    Console.WriteLine("Erro ao consultar o CEP: " + e.Message);
                }
            }
        }
    }
}
