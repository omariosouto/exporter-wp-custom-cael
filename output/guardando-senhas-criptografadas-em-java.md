---
title: "Guardando senhas criptografadas em Java"
date: "2010-06-18"
author: "ricardo.nakamura"
authorEmail: "ricardo.nakamura@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Eu e o Thiago Ferreira estavámos mais uma vez na Caelum passando pela situação de gravar as senhas do usuário no banco de dados. Ainda hoje alguns [grandes sites cometem o grave erro de guardar as senhas dos usuários em texto puro](http://blog.moertel.com/articles/2006/12/15/never-store-passwords-in-a-database), fazendo com que um possível roubo de dados acarrete num problema ainda maior.

O processo clássico é guardar um hash (chamado também de _digest_ nesse caso) da senha do usuário, usando algum algoritmo de hash unidirecional. Isso pode ser feito utilizando uma chamada de função na query do banco de dados (como `MD5()`no MySQL), ou, o mais utilizado para não ter de trafegar a senha entre o servidor web e o banco de dados: com o `MessageDigest` do `javax.security`. Através dessa classe você pode facilmente gerar o hash de uma senha:

\[java\] MessageDigest algorithm = MessageDigest.getInstance("MD5"); byte messageDigest\[\] = algorithm.digest("senha".getBytes("UTF-8")); \[/java\]

Agora temos um array de bytes que podemos guardar no banco de dados. Quando o usuário logar, basta digerirmos novamente a senha colocada no formulário web, e comparar o hash resultante com o que há no banco. Você poderia guardar esse array de `byte` como uma `String` fazendo `new String(bytes, encoding)`, porém muito mais usual é guardar os hexadecimais desses bytes dentro de uma `String`:

\[java\] StringBuilder hexString = new StringBuilder(); for (byte b : messageDigest) { hexString.append(String.format("%02X", 0xFF & b)); } String senha = hexString.toString(); \[/java\]

Isso gerará `e8d95a51f3af4a3b134bf6bb68a213a`.

Apesar de muito usado, [o MD5 já é considerado um algoritmo de hash quebrado](http://www.schneier.com/blog/archives/2005/03/more_hash_funct.html), pois hoje em dia podemos rapidamente, através de força bruta, descobrir uma String que gere o mesmo hash, já que neste algoritmo ocorrem mais colisões do que o que foi inicialmente imaginado por seus criadores. Muitos passaram a usar o SHA-1, porém este [já da sinais de que, num futuro próximo, será quebrado](http://www.schneier.com/blog/archives/2005/02/sha1_broken.html) com força bruta. O próprio [governo americano já evita utilizar a família SHA-1](http://csrc.nist.gov/groups/ST/hash/policy.html), priorizando o uso do SHA-2. Logo, o procedimento completo mais adequado atualmente seria usar algo como:

\[java\] MessageDigest algorithm = MessageDigest.getInstance("SHA-256"); byte messageDigest\[\] = algorithm.digest(original.getBytes("UTF-8"));

StringBuilder hexString = new StringBuilder(); for (byte b : messageDigest) { hexString.append(String.format("%02X", 0xFF & b)); } String senha = hexString.toString(); \[/java\]

Para tornar seu sistema ainda mais seguro em relação as senhas guardadas, [pode-se ainda adicionar salts, mais iterações de hash e outras técnicas](http://www2007.org/poster855.php) que tornaria ainda mais difícil um possível ataque de força bruta contra uma base de dados roubada, se protegendo também contra o provável uso de senhas fracas por parte de seus usuários.
