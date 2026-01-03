##  Ubuntu LTS setup
1. Install Ubuntu Server 22.04 or 24.04 LTS and choose OpenSSH during installation.
2. After first login, update system and enable the firewall with SSH allowed.

  sudo apt update
  sudo apt upgrade -y
  sudo ufw allow OpenSSH
  sudo ufw enable

3. Set a static IP or DHCP reservation via our IT team, then record the hostname for students.

4. install R and RStudio Server, using the latest RStudio Server .deb from Posit.

  sudo apt install -y r-base gdebi-core
  # download rstudio-server-*.deb,
  then:
  sudo gdebi rstudio-server-*.deb

5. Create a shared renv cache so packages install once and are reused across students.

  sudo mkdir -p /opt/R/renv/cache
  sudo chown -R root:rstudio /opt/R/
  renv/cache
  echo 'RENV_PATHS_CACHE=/opt/R/renv/
  cache' | sudo tee -a /etc/R/
  Renviron.site
  sudo rstudio-server restart

6. Create a Unix group per course and add students as system users.

  sudo groupadd psych434
  sudo adduser alice
  sudo usermod -a -G psych434 alice

7. If we have a username list in students.txt, create accounts in bulk.

  while read -r u; do
    sudo adduser --disabled-password
  --gecos "" "$u"
    sudo usermod -a -G psych434 "$u"
  done < students.txt

8. Create a course directory with
     group permissions and the setgid
     bit for shared ownership.

  sudo mkdir -p /srv/courses/psych-434-
  2025
  sudo chown -R root:psych434 /srv/
  courses/26-434
  sudo chmod -R 2770 /srv/courses/
  26-434 

 9. Place the 26-434 project inside /srv/courses/26-434, and keep renv.lock committed.

 10. Add margot to the project with renv, then snapshot once from my account.

  renv::init()
  renv::install("margot")
  renv::snapshot()

  11. Share this onboarding note with
     students.

  1. Connect to the university network
  with Cisco Secure Client.
  2. Open RStudio Server at https://
  <hostname-or-ip>:8787 and log in.
  3. Open the course project in /srv/
  courses/26-434.
  4. Run renv::restore() once and wait
  for packages to install.
  5. If you see an error, copy the full
  console output and send it to me.

  12. next, TODO get Ubuntu version, hostname choice, and whether we want HTTPS with a local certificate.
