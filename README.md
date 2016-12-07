# juliandson_php

<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>
    <body>
<?php
    
    
	$nome_servidor = "localhost";
    $nome_usuario = "root";
    $senha = "";
	$nome_banco = "Bancoconversa1";
    
	// Criar conexão
    $conecta = new mysqli($nome_servidor,$nome_usuario,$senha,$nome_banco);
    // Verificar Conexão
    if ($conecta->connect_error) {
    die("Conexão falhou: " . $conecta->connect_error."<br>");
    }
    echo "Conexão realizada com sucesso <br>";
    
	
 
     $sql = "SELECT id,conversa, megasena FROM conversa";
		$resultado = $conecta->query($sql);
			if ($resultado->num_rows > 0) {
 // saída dos dados
		
		while($linha = $resultado->fetch_assoc()) {
			 echo "id: " . $linha["id"]. " - Name: " . $linha["conversa"]. " " . $linha["megasena"]. "<br>";
			}
			}else {
			echo "0 results";
			}

	
	$url=file_get_contents('https://api.telegram.org/bot267280014:AAHI1LpAoTNse_37pLe0z0kj70bn7KGJrKM/getUpdates');
    $x=json_decode($url,true);
    $xLen=count($x['result']);
	
	for ($i=0;$i<$xLen;$i++) //capturando dados do JSON
	{
		$idUpdate[$i] = $x['result'][$i]['update_id'];	
        $idChat[$i] = $x['result'][$i]['message']['chat']['id'];
        $text[$i] = $x['result'][$i]['message']['text'];
    }
	
	$file = "idUpdate.txt"; //criando arquivo de texto
	$str = file_get_contents($file);
    $arrFile =  explode( ',', $str);
	
	for ($i = 1; $i <= 6; $i++) //gera os 6 numeros da megaSena
	{
        $n[] = str_pad(rand(1, 60), 2, '0', STR_PAD_LEFT); 
    }
    sort($n); //ordena os números
    $mega = implode(' - ', $n);//exibe os numeros
	
	for ($i=0;$i<$xLen;$i++) 
	{
		if (!in_array($idUpdate[$i], $arrFile))
		{
			if ($text[$i] == '/megaSena') 
			{
				$url = 'https://api.telegram.org/bot267280014:AAHI1LpAoTNse_37pLe0z0kj70bn7KGJrKM/sendMessage?chat_id='.$idChat[$i]."&text=$mega";
				file_get_contents($url);
				file_put_contents($file, $idUpdate[$i].",", FILE_APPEND | LOCK_EX);
				print ($mega);
				$sql = "INSERT INTO conversa (id,conversa,megasena)
                        VALUES ('$idUpdate[$i]','$idChat[$i]','$mega',1)";
                if ($conecta->query($sql) === TRUE) {
                echo "Novo registro criado com sucesso";
                } else {
                echo "Erro: " . $sql . "<br>" . $conecta->error."<br>";
                }

		    mysqli_query($conecta,"insert into boot values ($idUpdate[$i],$idChat[$i],$mega,1);");
			}
		}
	}
	$conecta->close();
?>

    </body>
</html>
