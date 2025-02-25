Integração com Mercado Pago (PIX e Boleto) sem o SDK

Este projeto permite a integração com a API do Mercado Pago para gerar pagamentos via PIX e boleto, além de verificar o status do pagamento.


### Requisitos

- PHP 7.4 ou superior.
- Token de acesso do Mercado Pago (access_token). (https://www.youtube.com/watch?v=gwqPNEW0xVY)
- URL de notificação para receber atualizações de pagamento.

### Como Usar

Crie uma instância da classe MercadoPago passando o access_token e a URL de notificação.
   
 ```php
   $access_token = 'SEU_ACCESS_TOKEN';
   $notification_url = 'http://seusite.com/notificacao.php';
   $mp = new MercadoPago($access_token, $notification_url);
```

Defina as informações do pedido
 ```php
$order_data = [
    'ref' => 12345, // Referência única do pedido
    'amount' => 50.00, // Valor do pagamento
    'description' => 'Pagamento Teste', // Descrição do pagamento
    'payer_email' => 'mat.mcd7@gmail.com', // E-mail do pagador
    'payer_first_name' => 'Mateus', // Nome do pagador (opcional)
    'payer_last_name' => 'Junior', // Sobrenome do pagador (opcional)
    'payer_cpf' => '12345678909' // CPF do pagador (obrigatório para boleto)
];
```

Gerar um Pagamento PIX:

 ```php
$result_pix = $mp->generatePayment($order_data, 'pix');

if (isset($result_pix['error'])) {
    echo "Erro ao gerar pagamento PIX: " . $result_pix['error'];
} else {
    echo "QR Code (base64): <img src='data:image/jpeg;base64, " . $result_pix['qr_code_base64'] . "' width='200' />";
    echo "<br>Ou copie o código Pix: " . $result_pix['qr_code'];
    echo "<br><a href='" . $result_pix['payment_url'] . "'>Clique aqui para pagar</a>";
}
```

Gerar um Boleto:

 ```php
$result_boleto = $mp->generatePayment($order_data, 'bolbradesco');

if (isset($result_boleto['error'])) {
    echo "Erro ao gerar boleto: " . $result_boleto['error'];
} else {
    echo "Boleto gerado com sucesso!";
    echo "<br><a href='" . $result_boleto['boleto_url'] . "'>Clique aqui para visualizar o boleto</a>";
    echo "<br>Código de barras: " . $result_boleto['boleto_barcode'];
}
```

Verificar Status do Pagamento:
 ```php
$payment_id = '123456789'; // Substitua pelo ID do pagamento recebido no post da notificação 
$payment_info = $mp->getPayment($payment_id);

if (isset($payment_info['error'])) {
    echo "Erro ao verificar pagamento: " . $payment_info['error'];
} else {
    //Aqui você pode fazer sua lógica de entrega do pedido
    $status = $payment_info['status'];
    $ref = $payment_info['external_reference'];
 
    if ($status === 'approved') {
        echo "Pagamento aprovado.";
    }
}
```
