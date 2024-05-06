Vagrant.configure("2") do |config|
  # Configuration pour devapp01
  config.vm.define "devapp01" do |devapp01|
    devapp01.vm.box = "bento/ubuntu-22.04"
    devapp01.vm.network "private_network", ip: "172.16.238.10"
    devapp01.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y openjdk-11-jdk tomcat9 git
      # Configurer Tomcat et Git ici
    SHELL
    # Créer un cron pour sauvegarder le code
    devapp01.vm.provision "shell", inline: "echo '0 * * * * rsync -avz /path/to/code 172.16.238.12:/backup/' | crontab -"
  end

  # Configuration pour dbapp01
  config.vm.define "dbapp01" do |dbapp01|
    dbapp01.vm.box = "bento/ubuntu-22.04"
    dbapp01.vm.network "private_network", ip: "172.16.238.11"
    dbapp01.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y postgresql postgresql-contrib
      # Configurer PostgreSQL pour qu'il écoute seulement sur l'interface interne
      sudo sed -i "/^#listen_addresses = 'localhost'/c\listen_addresses = '172.16.238.10'" /etc/postgresql/12/main/postgresql.conf
      sudo systemctl restart postgresql
    SHELL
    # Créer un cron pour sauvegarder la base de données
    dbapp01.vm.provision "shell", inline: "echo '*/30 * * * * pg_dumpall -h 172.16.238.11 -U postgres | gzip > /backup/db_backup_$(date +\\%Y\\%m\\%d\\%H\\%M).gz' | crontab -"
  end

  # Configuration pour backup01
  config.vm.define "backup01" do |backup01|
    backup01.vm.box = "bento/ubuntu-22.04"
    backup01.vm.network "private_network", ip: "172.16.238.12"
    # Pas de logiciel spécifique requis pour le backup
  end
end
