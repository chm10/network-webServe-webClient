/*25 de marco de 2017 - Disciplina de Redes de Computadores
Implementado por Christian Maekawa

Título do Trabalho : Servidor Web
Desenvolver um Servidor Web simples que:
Manipule uma requisição HTTP - Ok
- Aceite a requisição - Ok
- Analise o cabeçalho - Ok
- Obtenha um arquivo requisitado do sistema de arquivo do servidor - Ok
- Crie uma mensagem de resposta HTTP: - Ok
- Linhas de cabeçalho + arquivo  - Ok
- Envie a resposta para o cliente -Ok
- Após criar o servidor, você deve requisitar um arquivo usando um browser (ex.: FIREFOX), e o browser deverá apresentar corretamente o arquivo enviado pelo seu servidor. - Ok
 

*/

#include <stdio.h>// FILE fopen(), fseek(),fread() ftell() ,rewind () puts() ...
#include <string.h>//memset() strlen() e malloc ()
#include <sys/socket.h> //socket()
#include <arpa/inet.h> // para sockaddr_in , INADDR_ANY, htons() inet_ntoa()
#include <unistd.h>// para o write() e close() 

int main (int arc, char * argv[]){
	int socket_ouvido, novo_socket , tamanhoDoEndereco, conexoes;
	struct sockaddr_in server, client;
	char * recadinho;
	conexoes = 0;//conta numero de conexoes
	/*Leitura do arquivo html*/
	char * buffer;
	int buffersize = 1024;
	buffer = (char*) malloc(buffersize);    
  if(!buffer){
  	printf("ERRO: Nao ha memoria suficiente para ser alocado!\n");
  	return 1;    
  }

	socket_ouvido =  socket (AF_INET, SOCK_STREAM, 0);
	if (socket_ouvido == - 1){
		printf ("FALHA: Infelizmente nao foi possivel criar seu socket.\n");
		return 1;
	}
	printf ("Socket criado com sucesso!");
	memset(&server, 0, sizeof(server));//aloca memoria com zeros
	server.sin_family = AF_INET;//Dominio
  server.sin_addr.s_addr = INADDR_ANY;//Endereco
  server.sin_port = htons( 8888 );//porta
  
	if (bind(socket_ouvido,(struct sockaddr *) & server ,sizeof(server)) == -1){
		puts("FALHA: Infelizmente a conexao falhou.\n");
			return 1;
	}
	
	//
	long tamanhoDoArquivo;
	FILE * arquivo = fopen("index.html","rb");
	if (!arquivo){
		puts("FALHA: Encontramos um problema ao tentar abrir o !\n");
		return 1;
	}
		puts(" aberto com sucesso!\n");
	//Indica a posicao do ponteiro
	if (fseek(arquivo,0,SEEK_END) == -1){
		puts(" nao pode ser sincronizado\n");
		return 1;
	}
	//Indica o valor do ponteiro
	tamanhoDoArquivo = ftell(arquivo);
	if (tamanhoDoArquivo == -1){
		puts("FALHA: Houve perda de pacote e o programa teve que ser finalizado\n");
		return 1;
	}
		rewind(arquivo);//posiciona o ponteiro
	
	char *msg = (char *) malloc(tamanhoDoArquivo);
	if (!msg){
		puts("FALHA: O buffer do  nao esta alocado\n");
		return 1;
	}
	if (fread(msg, tamanhoDoArquivo, 1, arquivo) != 1){
    puts("FALHA: O  nao esta legivel\n");
    return 1;
  }
  fclose(arquivo);
	printf("O tamanho do  eh:  %ld\n", tamanhoDoArquivo);

	puts("Sucesso: Conexao realizada com Sucesso !\n");
	if (listen(socket_ouvido, 10) == -1){
        puts("FALHA: O servido nao esta recebendo solicitacoes\n");    
        return 1;   
  }
	puts ("Aguardando os pedidos de conexoes... \n");
	tamanhoDoEndereco= sizeof (struct sockaddr_in);
	recadinho = "HTTP/1.1 200 OK\r\n";
	while( (novo_socket = accept(socket_ouvido, (struct sockaddr *)&client, (socklen_t*)&tamanhoDoEndereco)) ){
        printf("%d conexoes aceitas\n",++conexoes);
				printf("Um cliente esta conectado de %s:%hu...\n", inet_ntoa(server.sin_addr), ntohs(server.sin_port));
        if(!write(novo_socket , recadinho , strlen(recadinho))){
            close(novo_socket);
            continue;
        }
        char clen[40];
        sprintf(clen, "Content-length: %ld\r\n", tamanhoDoArquivo);
        if (!write(novo_socket, clen,strlen(clen))){
            close(novo_socket);
            continue;
        }
        if (!write(novo_socket, "Connection: close\r\n\r\n",strlen("Connection: close\r\n\r\n"))){
            close(novo_socket);
            continue;
        }
        if (!write(novo_socket, msg, tamanhoDoArquivo)){
            close(novo_socket);
            continue;
        }

        printf("Arquivo foi enviado com sucesso!\n");
        close(novo_socket);  
    }
    if (novo_socket<0)
    {
        perror("accept failed");
        close(novo_socket);
        return 1;
    }
   	close(socket_ouvido);
	return 0;
}
