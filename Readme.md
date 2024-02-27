# Tarea: Almacenamiento



## Descripción


- Crear un depósito S3 público para almacenar imágenes

- Crear una instancia de BD administrada (BD, usuario, con acceso solo desde una de tus instancias).
NOTA: Recuerda seleccionar una instancia de la capa gratuita para no incurrir en gastos innecesarios

Opcionalmente, crea en la máquina virtual una aplicación que muestre la imágenes del depósito S3, que se conecte a la BD y que permita subir imágenes al depósito.


### Código de Ayuda

El siguiente código puede ayudarte, si creas la aplicación con PHP.

##### connect_db.php

```php
<!DOCTYPE html>

<html>
        <head>
                <title>Mi blog</title>
                <meta charset="UTF8" />
        </head>

        <body>
                <h1>Welcome to my blog</h1>
                <img src="https://storage.googleapis.com/BUCKETNAME/FILEIMG.jpg" />
                <p>
                <?php
                  $dbserver = $_ENV['DB_HOST'],;
                  $dbuser = $_ENV['DB_USER'],;
                  $dbpassword = $_ENV['DB_PASS'],;
                  // Munca exponga claves en un documento raiz

                  $conn = new mysqli($dbserver, $dbuser, $dbpassword);

                  if(mysqli_connect_error()) {
                        echo ("Database not available, ERROR: " . mysqli_connect_error());
                  } else {
                        echo ("Database connection was successful.");
                  }
                 ?>
                 </p>

         <p><a href="index.html">Back to home</a></p>

        </body>

</html>
```

##### list_buckets.php

```php
<h1>List Buckets</h1>
<?php


require 'vendor/autoload.php';

use Aws\S3\S3Client;


$p = getenv('AWS_SESSION_TOKEN');
echo "<p>" . $p . "</p>";


$s3Client = new S3Client([
    //'profile' => 'default',
    'region' => 'us-east-1',
    'version' => 'latest',
    'credentials' => [
        'key'    => $_ENV['AWS_KEY'],
        'secret' => $_ENV['AWS_SECRET'],
        'token' => $_ENV['AWS_TOKEN'] 
    ]
]);

//Listing all S3 Bucket
$buckets = $s3Client->listBuckets();
foreach ($buckets['Buckets'] as $bucket) {
    echo "<p>1.";
    echo $bucket['Name'] . "\n";
    echo "</p>";
}
```

##### upload.php

```php
<?php
	if(isset($_FILES['image'])){
		$file_name = $_FILES['image']['name'];   
		$temp_file_location = $_FILES['image']['tmp_name']; 

		require 'vendor/autoload.php';

		$s3 = new Aws\S3\S3Client([
			'region'  => '-- your region --',
			'version' => 'latest',
			'credentials' => [
				'key'    => "-- access key id --",
				'secret' => "-- secret access key --",
			]
		]);		

		$result = $s3->putObject([
			'Bucket' => '-- bucket name --',
			'Key'    => $file_name,
			'SourceFile' => $temp_file_location			
		]);

		var_dump($result);
	}
?>

<form action="<?= $_SERVER['PHP_SELF']; ?>" method="POST" enctype="multipart/form-data">         
	<input type="file" name="image" />
	<input type="submit"/>
</form>   
```