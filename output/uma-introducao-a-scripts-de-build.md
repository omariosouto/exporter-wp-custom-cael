---
title: "Uma introdução a scripts de build"
date: "2010-11-10"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O processo de build é o coração das [práticas de engenharia ágil](http://www.caelum.com.br/curso/pm-87-agile-praticas-ageis/): é através dele que [automatizamos todos os passos necessários](https://blog.caelum.com.br/o-processo-de-deploy-continuo/) para garantir a qualidade mínima esperada.

Esse nível de garantias varia entre cada projeto e todos os passos que uma equipe madura seria capaz de executar com quase perfeição são automatizados para minimizar falhas e atingir tal objetivo.

Diversas práticas são utilizadas para garantir a qualidade necessária que [nos permite executar entregas contínuas](https://blog.caelum.com.br/o-processo-de-deploy-continuo/), e a maior parte delas estão presentes ou são referenciadas dentro do processo automatizado de build.

Um processo de build simples envolve a compilação - quando necessária - e geração de algum artefato final. O exemplo a seguir mostra um script do ant compilando seus arquivos java:

\[xml\] <project name="project-name" default="compile"> <description>Build full</description> <property file="build.properties" /> <property name="war.file" value="${war.filename}.war" />

<path id="libs.classpath" path="."> <fileset dir="${libs.dir}"> <include name="\*.jar" /> </fileset> </path>

<path id="compile.classpath"> <path refid="servlet-api.classpath" /> <path refid="libs.classpath" /> </path>

<target name="compile" depends="prepare" description="--> compile the classes"> <javac destdir="${tmp.classes.dir}" srcdir="${src.dir}" classpathref="compile.classpath" debug="true" encoding="UTF-8"> </javac> </target>

</project> \[/xml\]

Lembrando que nesse mundo para a web em Java, é bem comum que o artefato final seja um arquivo .war a ser deployado em algum servidor de aplicação ou servlet container. Para criá-lo usamos outras tags simples do ant:

\[xml\] <target name="war" depends="compile" description="--> generate project's war"> <delete file="${artifacts.dir}/${war.file}" /> <copy todir="${tmp.classes.dir}"> <fileset dir="${resources.dir}" /> </copy>

<war destfile="${artifacts.dir}/${war.file}" webxml="${webapp.dir}/WEB-INF/web.production.xml" compress="true" > <fileset dir="${webapp.dir}"> <exclude name="WEB-INF/web\*.xml" /> <exclude name="\*\*/servlet\*.jar" /> </fileset> <classes dir="${tmp.classes.dir}" /> </war> </target> \[/xml\]

E, em Ruby, [o artefato comum é uma gem](http://simplesideias.com.br/criando-sua-propria-rubygem/), que podemos criar utilizando a task GemPackage:

\[ruby\] Rake::GemPackageTask.new(spec) do |pkg| pkg.gem\_spec = spec end \[/ruby\]

Um script completo escrito para Rake, incluindo a tag de geração de pacote e a descrição de sua gem como a seguir [pode ser criada automaticamente também](https://github.com/technicalpickles/jeweler):

\[ruby\] require 'rake' require 'rake/testtask' require 'rake/rdoctask' require 'rake/gempackagetask'

desc 'Default: build gem.' task :default => :package

PKG\_FILES = FileList\[ '\[a-zA-Z\]\*', 'generators/\*\*/\*', 'lib/\*\*/\*', 'rails/\*\*/\*', 'tasks/\*\*/\*', 'test/\*\*/\*' \]

spec = Gem::Specification.new do |s| s.name = "Relata" s.version = "0.0.2" s.author = "Anderson Leite, Guilherme Silveira" s.email = "anderson.leite@caelum.com.br" s.homepage = "http://github.com/caelum/relata" s.platform = Gem::Platform::RUBY s.summary = "Helps poking around with relationships when using ARel" s.files = PKG\_FILES.to\_a s.require\_path = "lib" s.has\_rdoc = false s.extra\_rdoc\_files = \["README.markdown"\] end

desc 'Turn this plugin into a gem.' Rake::GemPackageTask.new(spec) do |pkg| pkg.gem\_spec = spec end \[/ruby\]

Para sistemas legados que ainda não possuem script, o primeiro passo é a compilação e geração de tal artefato. Dessa maneira o desenvolvedor não esqueçe de copiar arquivos de configuração durante o processo de montagem (assembly) de seu artefato, um erro muito comum em processos de build manuais. A simples adição de uma tag evita erros simples causados por descuido humano como o anterior:

\[xml\] <copy todir="${tmp.classes.dir}"> <fileset dir="${resources.dir}" /> </copy> \[/xml\]

Com o amadurecimento da equipe nesse sistema legado, ou com uma equipe qualquer trabalhando em um sistema novo - [sem nenhuma restrição, green field](http://en.wikipedia.org/wiki/Greenfield_project), visamos a automatização de todas as práticas que dependem de um ser humano, um processo que inclue outros passos.

Alternativas de ferramentas de build que vêm ganhando espaço ultimamente são [o gradle](http://gradle.org/) e [o buildr](http://buildr.apache.org/), [e o maven](https://blog.caelum.com.br/processo-de-build-com-o-maven), todos correndo atrás de suportar o maior número possível de linguagens na JVM.

Com pouco esforço tanto em sistemas já existentes quanto em novos projetos, compile e gere artefatos com **todos** os arquivos de configuração automaticamente.
