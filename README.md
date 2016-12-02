--ALUNO: ARTHUR ALBERTO GUEDES



a = (arg[1]) 																	--recebendo arquivo txt no terminal


--A seguir estão expressões regulares que possuem a forma das expressões/estruturas de um programa em DOOL.

palavra = "(%a+%d*%a*%d)*" 														--definição de palavra "%a+%d*%a*%d*"
def_variavel1 = "(var) (%a+%d*%a*%d*)( : number)"								--definição de declaração de variável
atribuicao_variavel = "(%a+%d*%a*%d*) (=) (%d+)"								--definição de atribuição de variável
inicio_programa = "program"														--definição de começo do programa DOOL
printar_variavel = "(io.print)(%()(%a+%d*%a*%d*)(%))"							--definição do campo io.print
def_variavel2 = "(var) (%a+%d*%a*%d*)(:number)"									--definição de declaração de variável sem espaço
def_classe = "(class) (%a+%d*%a*%d*) (as)"										--definição de classe
variavel_classe = "(attribute) (%a+%d*%a*%d*)( : number)"						--definição de variável de classe
def_funcao = "(def) (static) (%a+%d*%a*%d*) (as)"								--definição de função	
chamada_objeto = "(%a+%d*%a*%d*) (= new) (%a+%d*%a*%d*)"						--definição de chamada de objeto
parametro_funcao = "(%a+) (:) (number)"											--definição dos parametros da função
return_funcao = "(return) (%a+%d*%a*%d*)"										--definição do return da função	
chama_funcao = "(%a+%d*%a*%d*).(%a+)(%()(%a+%d*%a*%d*)(%))"						--definição de chamada da função


--Variáveis de controle

local controle_chaves = 1														--variável de controle utilizada para fechar as chaves corretamente no programa em C
local funcao_ativa = 0															--variável usada para separar as declarações de variáveis e declarações de parametros, já que possuem a mesma expressão regular
local verificador_tabela = 0													--variável usada para indicar que as declarações feitas são de uma função
recebe_funcao = {}																--tabela que recebe a função de dentro da estrutura e a coloca fora
recebe_parametro = {}															--tabela que recebe os parametros na chamada do método e coloca os valores dentro da função, para não haver a perda desses valores 



--variáveis auxiliares usadas para percorrer a tabela

k = 0																			--iterador sobre a tabela 'recebe_funcao'
i = 0								




local nomeArquivo = "prog.c"
arquivo = assert(io.open(nomeArquivo, 'w'), "Arquivo não pode ser criado") 		--Criando o arquivo prog.c
arquivo:write("#include <stdio.h>\n")											--Colocando os includes necessários para o prog.c funcionar
arquivo:write("#include <stdlib.h>\n")




for line in io.lines(a) do               										--iterador que percorre as linhas do arquivo de entrada
  
local linhaatual = line 								 						--recebe o que está escrito na linha atual


--A partir daqui é feito o reconhecimento da linha recebida no arquivo; através de comparações, descobre a qual expressão regular pertence e faz a devida modificação para estar de acordo com a linguagem C
 
	if(string.match(linhaatual,def_variavel1) and verificador_tabela == 0)then	--se ocorre a correspondencia, pega o valor do identificador da variavel
			mat1,mat2,mat3 = string.match(linhaatual,def_variavel1)				--recebe as capturas definidas na expressão regular def_variavel
			
			arquivo:write("int " .. mat2 .. ";\n")	
	end	
	
	
	
	--Reconhece definição de variável em funções
	if(string.match(linhaatual,def_variavel1) and verificador_tabela == 1)then	  		
			mat1,mat2,mat3 = string.match(linhaatual,def_variavel1)				
				
			recebe_funcao[k] = ("int " .. mat2 .. " ;\n")
			k = k +1 
	end
	
	
	
	
	--Reconhece definição de variável na main
	if(string.match(linhaatual,def_variavel2))then								
			mat1,mat2,mat3 = string.match(linhaatual,def_variavel2)				
			
			arquivo:write("int " .. mat2 .. ";\n")	
	end
	
	
	
	--Reconhece atribuição de valores á variáveis na main	
	if(string.match(linhaatual,atribuicao_variavel) and verificador_tabela == 0)then	
			mat1,mat2,mat3 = string.match(linhaatual,atribuicao_variavel)				
			
			arquivo:write(mat1 .. mat2 .. mat3 .. ";\n")
	end		

	
	
	--Reconhece atribuição de variáveis em Classes
	if(string.match(linhaatual,atribuicao_variavel) and verificador_tabela==1)then
			
			mat1,mat2,mat3 = string.match(linhaatual,atribuicao_variavel)
			recebe_funcao[k] = (mat1 .. " = " .. mat3 .. ";\n")
			k = k +1 
	end
	
	
	
	--Reconhece o início de um programa DOOL
	if(string.match(linhaatual,inicio_programa))then
			arquivo:write("int main(){\n")
			controle_chaves = 3
	end
	
	
	
	--Reconhece a função io.print em Classes	
	if(string.match(linhaatual,printar_variavel) and verificador_tabela==1)then
			mat1,mat2,mat3,mat4 = string.match(linhaatual,printar_variavel)
		
			recebe_funcao[k] = ('printf("%d\ \\' .. 'n",' .. mat3 .. ");\n")
			
			k = k+1
	end			
	
	
	
	--Reconhece a função io.print na main
	if(string.match(linhaatual,printar_variavel) and verificador_tabela==0)then
			mat1,mat2,mat3,mat4 = string.match(linhaatual,printar_variavel)
			
			arquivo:write('printf("%d\ \\' .. 'n",' .. mat3 .. ");\n")
			
	end	
	
	
	
	--Reconhece uma definição de classe
	if(string.match(linhaatual,def_classe))then
			mat1,mat2 = string.match(linhaatual,def_classe)
			arquivo:write("struct " .. mat2 .. " {\n")
			
			
			
	end
	
	
	
	--Reconhece uma declaração de variavel em classes
	if(string.match(linhaatual,variavel_classe))then
			mat1,mat2,mat3= string.match(linhaatual,variavel_classe)
			arquivo:write("int " .. mat2 .. ";\n")
			
	end		
	
	

	
	--Reconhece uma declaração de função
	if(string.match(linhaatual,def_funcao))then
			func1,func2,func3,func4= string.match(linhaatual,def_funcao)
			arquivo:write("int " .. "(*" .. func3 .. ")(int);\n" )
			
			recebe_funcao[k] = ("int " .. func3 .. "(" .. "int " .. mat2 .. ") {\n")			--Recebe o conteúdo da função daquela linha
			recebe_parametro[i] = mat2															--Recebe o parametro para posteriormente enviá-lo para a função em C
			i = i + 1
			k = k+1
			
			--Atualização das variáveis de controle
			controle_chaves = 2
			funcao_ativa = 1
			verificador_tabela = 1
			
	end
	
	
	--Reconhece a linha dos parametros da função
	if(string.match(linhaatual,parametro_funcao) and funcao_ativa == 1)then
			mat1,mat2,mat3= string.match(linhaatual,parametro_funcao)
			
			--Aqui é feita a atribuição dos parametros a variável declarada em C, para que os valores não sejam perdidos
			for i=0,#recebe_parametro do
			recebe_funcao[k] = ("int " .. mat1 .. " = " .. recebe_parametro[i] .. ";\n")
			end
			k = k + 1
						
			funcao_ativa = 0;
			verificador_tabela = 1													--Indica que as declarações estão sendo para uma função
			
			recebe_parametro[i] = mat1
			i = i + 1
			
	end
	
	--Reconhece um return de função
	if(string.match(linhaatual,return_funcao))then
			aux1,aux2 = string.match(linhaatual,return_funcao)
			
			recebe_funcao[k] = ("return " .. aux2 .. ";\n")
			
			k = k + 1
	end
	
	
			--Reconhece os "ends"; Através da variável de controle estabele qual chave é a certa a ser utilizada
			if(string.match(linhaatual,"end") and controle_chaves==1)then
					arquivo:write("};\n")
					controle_chaves = 3
					for i = 0,#recebe_funcao do
					arquivo:write(recebe_funcao[i])
					end
					k = 0
					verificador_tabela = 0	
					
			
	
			else if(string.match(linhaatual,"end") and controle_chaves==2)then
					
					controle_chaves = 1
					recebe_funcao[k] = ("}\n")
					k = k + 1
			else if(string.match(linhaatual,"end") and controle_chaves==3)then
					
			end
			end
			end
	
	
	
	--Reconhece uma chamada de objeto
	if(string.match(linhaatual,chamada_objeto))then
			mat1,mat2,mat3= string.match(linhaatual,chamada_objeto)
			arquivo:write("struct " .. mat3 .. (" ") ..  mat1 .. ";\n")
	end	
	
	
	--Faz a conversão da chamada de objeto para a função em C
	if(string.match(linhaatual,chama_funcao) and funcao_ativa == 0)then
			
			mat1,mat2,mat3,mat4,mat5= string.match(linhaatual,chama_funcao)
			if(mat1 == "io")then
			else
			arquivo:write(mat1 .. "." .. mat2 .. "=" .. mat2 .. "(" .. mat4 .. ");\n")
			
	end	
	end
	
	

	
	
end 																--Fim do laço que percorre o arquivo



arquivo:write("return 0;\n" .. "}")	 --Completa com o return 0 da função main







