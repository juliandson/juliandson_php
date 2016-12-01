# juliandson_php

<?php
     
	$website = "https://api.telegram.org/bot242937400:AAEI-7AdySH1DEwr3m2MBWEIq5WyhTLhv48/getUpdates/getUpdates";
    $update = file_get_contents($website);
	
	function sendMessage($id,$texto){
		$website1 = "https://api.telegram.org/bot242937400:AAEI-7AdySH1DEwr3m2MBWEIq5WyhTLhv48/sendMessage?chat_id=".$id."&text=".$texto;
		file_get_contents($website1);
	}
     $file = 'arquivo.txt';
	 $str = file_get_contents($file);
     $arrUpdateId =  explode( ',', $str );
	   
	   
	   
	$x = json_decode($update, true);
	$len = count($x['result']);
	
	$ids = array();
	
	for ($i = 0; $i < $len; $i++) {
		$text =  $x['result'][$i]['message']['text'];
		$id = $x['result'][$i]['message']['chat']['id'];
		$updateId = $x['result'][$i]['update_id'];
	    
		$texto1=preg_match('/^.*\/megaSena/',$text);
	     if (!in_array($updateId, $arrUpdateId)){
      
		if($texto1 == 1){
			
			for ($i = 1; $i <= 6; $i++) { 
				$n[] = str_pad(rand(1, 60), 2, '0', STR_PAD_LEFT); 
			}
			sort($n);
			$text =  implode(' - ', $n);
			
			sendMessage($id,$text);
			$file = 'arquivo.txt';
                    file_put_contents($file, $updateId.',', FILE_APPEND );
		}
		
	  }
	  
	}
?>
