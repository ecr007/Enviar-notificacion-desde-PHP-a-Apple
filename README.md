# Enviar-notificacion-desde-PHP-a-Apple

```php
/**
		 * @param Int $mensaje          -> Mensaje
		 * @param Int $id_dispositivo   -> Identificar el dispositivo en el servicio
		 */
		private function notificarIos($mensaje,$id_dispositivo){
			$ctx = stream_context_create();
			stream_context_set_option($ctx, 'ssl', 'local_cert', app_path().'/libs/pem/ckp.pem');
			stream_context_set_option($ctx, 'ssl', 'passphrase', 'yak6gedy');

			// Open a connection to the APNS server
			$fp = stream_socket_client(
				'ssl://gateway.push.apple.com:2195', $err,
				$errstr, 60, STREAM_CLIENT_CONNECT|STREAM_CLIENT_PERSISTENT, $ctx
			);

			if(!$fp){
				//exit("Failed to connect: $err $errstr" . PHP_EOL);
				return false;
			}

			// Create the payload body
			$body['aps'] = array(
				'alert' => $id_dispositivo. .$mensaje,
				'sound' => 'default',
				'badge' => 0
			);

			// Encode the payload as JSON
			$payload = json_encode($body);

			$msg = chr(0) . pack('n', 32) . pack('H*', $deviceToken) . pack('n', strlen($payload)) . $payload;

			// Send it to the server
			$result = fwrite($fp, $msg, strlen($msg));

			if (!$result){
				//echo 'Message not delivered' . PHP_EOL;
				// Close the connection to the server
				fclose($fp);
				return false;
			}
			else{
				//echo 'Message successfully delivered' . PHP_EOL;
				//echo $db->logMessage($userID,$recep,$estatus,$message);.
				// Close the connection to the server
				fclose($fp);
				return true;
			}
		}
 ```
