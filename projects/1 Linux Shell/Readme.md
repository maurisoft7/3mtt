# INTRODUCTION TO LINUX
Mastering Linux is fundamental essential as a DevOps engineer. This project equip me with a solid foundation in Linux.

## What is Linux?
Linux is a free, open-source operating system similar to Windows or MacOS, but it's more widely used for servers and supercomputers. It's known for its stability, security, and flexibility, allowing users to modify and distribute their version.

## Linux Distributions
Some of the Linux distribution are;
1. Ubuntu
2. CentOS
3. Debian
4. Fedora
5. RedHat

## Installtion and initial Setup
In this section I will be demostarting all the steps I took in creating a server (VM) in the cloud (AWS), and also how I gain access to it from my local enviroment (My laptop).
AWS, is a cloud provider to create the server in the cloud. AWS provide us with a free vitual server called EC2 ` **Elastic Compute Cloud** `.
I was able to create the server on AWS successfully by watching the 2 video provider by the instructor 

    1. [AWS account setup and Provisioning an Ubuntu Server](https://www.youtube.com/watch?v=xxKuB9kJoYM)

    2. [Connecting to your EC2 Instance](https://www.youtube.com/watch?v=TxT6PNJts-s)

and also the guildline provider in the course.

These are the steps involves:
1. I open my browser then go to AWS official website
![1. AWS Website](1.%20AWS%20Website.png)
2. I sign in into my AWS account because I already create an account with AWS in our previous project, by clicking on `Sign In to the Console`, then login as `Root user`, then I input my email address and my password.
![2. Sign in as a Root User](2.%20Signin%20as%20Root.png)
3. Below is my interface on AWS after I login,
![3. After Login to AWS](3.%20After%20Login%20to%20AWS.png)
Then I search for Elastic Cloud Compute (EC2) at the top left side 
![4. Search for EC2](4.%20Serach%20for%20EC2.png)
4. Then from the menu on the left side, I select `Instances`, then `Launch instances`.
![5. Launch instances](5.%20Launch%20instances.png)
5. Then I configure the instances by providing a name which is `Ubuntu_Server`, selecting Ubuntu as the OS image to use, creaing a key pair which I name `Ubuntu_Server_Key` the storage to be `20GB`.
![6. Configure Instances](6.%20Configure%20Instances.png)
6. Finally I click on `Launch Instances` and it was successful and running.
![7. Successfully Created](7.%20Ubuntu%20Created.png)
![8. Ubuntu Running](8.%20Ubuntu%20Running.png)


## Connecting to the Server
These are the steps I took to connect to my server on the cloud (AWS):

the client tools I used is `GitBash` which I have already installed on my laptop, the reason why I make use of GitBash is to allow me to use linux command like `chmod` `cd` and the likes.

1. I open my GitBash application on my laptop, then move to my `Download` folder using `cd ~/Downloads` and I was able to location the key I download from the AWS.
![9. GitBash](9,%20GitBash.png)
2. After that I ran `chmod 400 Ubuntu_Server_Key.pem` because Ubuntu_Server_Key.pem is the name of my key.
3. Then I went back to my new server that is running presently, to get my public ip address which happen to be `43.204.214.121`.
![10. Ip Address](10.%20Ip%20Address.png)
4. After that I went back to my GitBash application to continue my connection between my local laptop to the cloud environment. Then I ran `ssh -i Ubuntu_Server_Key.pem ubuntu@43.204.214.121` which connect me to my server on the cloud (AWS).
![11. GitBash Connection](11.%20Git%20Connection.png)

## Package Managers
The package manager I will be using is `apt-get` because what I install is under Debian family distribution `Ubuntu`

## Installing, Updating and Removing Software

1. The first thing I did was to update all my existing packages, the command I use is `sudo apt-get update`
![12. Update](12.%20Update.png)
2.  Installing Software Packages
I try to install `tree`, which is a command commonly used to visually see file system structure on a linux servers, so I installed the package with this command `sudo apt-get install tree`.
![14. Install Tree](14.%20Install%20Tree.png)
3. Verifying Installed Packages: then I ran `tree -L 1 /` to check if the package is install properly.
Note the L 1 means I only want the see level 1 because if I don't indicate that `tree` command will try to check all the directory one after the other which will take a lot of time.
![15. Confirm Tree](15.%20Comfirm%20tree.png)
4. Updating Installed Packages: After I finshed updating all the packages I also make sure to upgrade them to the latest version by running this command, `sudo apt-get upgrade -y`.
![13. Upgrade](13.%20Upgrading.png)
5. Removing Software Package: I try to remove `tree` package I installed earlier, with this command `sudo apt-get remove tree`
![16. Remove Tree](16.%20Remove%20Tree.png)


## Script here

#!/bin/bash

# AWS EC2 Web Server Deployment Script
# Automates Apache setup, static website deployment, and Git integration
# Author: Maurice
# Date: $(date +%Y-%m-%d)

# Configuration
SCRIPT_NAME="ec2-web-deployment"
LOG_FILE="/var/log/${SCRIPT_NAME}.log"
WEB_ROOT="/var/www/html"
SITE_NAME="my-website"
GIT_REPO="https://github.com/example/static-website.git"  # Replace with actual repo

# Color codes for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# Function to log messages
log_message() {
    local level=$1
    local message=$2
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo -e "[${timestamp}] ${level}: ${message}" | tee -a "$LOG_FILE"
}

# Function to print colored status
print_status() {
    local color=$1
    local message=$2
    echo -e "${color}${message}${NC}"
    log_message "STATUS" "$message"
}

# Function to update package lists
update_package_lists() {
    print_status "$BLUE" "Updating package lists..."
    if apt-get update -y >> "$LOG_FILE" 2>&1; then
        print_status "$GREEN" "Package lists updated successfully"
        return 0
    else
        print_status "$RED" "Failed to update package lists"
        return 1
    fi
}

# Function to install Apache web server
install_apache() {
    print_status "$BLUE" "Installing Apache web server..."
    
    if systemctl is-active --quiet apache2; then
        print_status "$YELLOW" "Apache is already installed and running"
        return 0
    fi
    
    if apt-get install apache2 -y >> "$LOG_FILE" 2>&1; then
        print_status "$GREEN" "Apache installed successfully"
        
        # Enable Apache to start on boot
        systemctl enable apache2 >> "$LOG_FILE" 2>&1
        
        # Start Apache service
        if systemctl start apache2 >> "$LOG_FILE" 2>&1; then
            print_status "$GREEN" "Apache service started successfully"
            return 0
        else
            print_status "$RED" "Failed to start Apache service"
            return 1
        fi
    else
        print_status "$RED" "Failed to install Apache"
        return 1
    fi
}

# Function to configure Apache for static website
configure_apache() {
    print_status "$BLUE" "Configuring Apache for static website..."
    
    # Create Apache configuration file
    local config_file="/etc/apache2/sites-available/${SITE_NAME}.conf"
    
    cat > "$config_file" << EOF
<VirtualHost *:80>
    ServerName ${SITE_NAME}
    DocumentRoot ${WEB_ROOT}/${SITE_NAME}
    
    ErrorLog \${APACHE_LOG_DIR}/${SITE_NAME}_error.log
    CustomLog \${APACHE_LOG_DIR}/${SITE_NAME}_access.log combined
    
    <Directory ${WEB_ROOT}/${SITE_NAME}>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
EOF

    # Enable the new site
    if a2ensite "$SITE_NAME.conf" >> "$LOG_FILE" 2>&1; then
        # Disable default site
        a2dissite 000-default.conf >> "$LOG_FILE" 2>&1
        
        # Enable required modules
        a2enmod rewrite >> "$LOG_FILE" 2>&1
        
        # Test configuration
        if apache2ctl configtest >> "$LOG_FILE" 2>&1; then
            systemctl reload apache2 >> "$LOG_FILE" 2>&1
            print_status "$GREEN" "Apache configuration applied successfully"
            return 0
        else
            print_status "$RED" "Apache configuration test failed"
            return 1
        fi
    else
        print_status "$RED" "Failed to enable Apache site"
        return 1
    fi
}

# Function to install and configure Git
install_git() {
    print_status "$BLUE" "Installing and configuring Git..."
    
    if command -v git &> /dev/null; then
        print_status "$YELLOW" "Git is already installed"
        return 0
    fi
    
    if apt-get install git -y >> "$LOG_FILE" 2>&1; then
        print_status "$GREEN" "Git installed successfully"
        
        # Configure Git (basic configuration)
        git config --global user.name "Web Server"
        git config --global user.email "webmaster@${SITE_NAME}"
        git config --global init.defaultBranch main
        
        print_status "$GREEN" "Git configuration completed"
        return 0
    else
        print_status "$RED" "Failed to install Git"
        return 1
    fi
}

# Function to create static website structure
create_website_structure() {
    print_status "$BLUE" "Creating website structure..."
    
    # Create website directory
    local site_dir="${WEB_ROOT}/${SITE_NAME}"
    
    if [ -d "$site_dir" ]; then
        print_status "$YELLOW" "Website directory already exists. Backing up..."
        mv "$site_dir" "${site_dir}.backup.$(date +%Y%m%d_%H%M%S)"
    fi
    
    mkdir -p "$site_dir"
    
    # Create homepage
    cat > "${site_dir}/index.html" << EOF
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Home - ${SITE_NAME}</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; }
        nav { background: #333; padding: 10px; }
        nav a { color: white; margin: 0 15px; text-decoration: none; }
        .container { max-width: 800px; margin: 0 auto; }
    </style>
</head>
<body>
    <nav>
        <a href="index.html">Home</a>
        <a href="about.html">About Us</a>
        <a href="contact.html">Contact</a>
    </nav>
    
    <div class="container">
        <h1>Welcome to ${SITE_NAME}</h1>
        <p>This is the homepage of our static website deployed on AWS EC2.</p>
        <h2>Server Information</h2>
        <ul>
            <li>Server Time: $(date)</li>
            <li>Hostname: $(hostname)</li>
            <li>IP Address: $(curl -s http://checkip.amazonaws.com)</li>
        </ul>
    </div>
</body>
</html>
EOF

    # Create about page
    cat > "${site_dir}/about.html" << EOF
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>About Us - ${SITE_NAME}</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; }
        nav { background: #333; padding: 10px; }
        nav a { color: white; margin: 0 15px; text-decoration: none; }
        .container { max-width: 800px; margin: 0 auto; }
    </style>
</head>
<body>
    <nav>
        <a href="index.html">Home</a>
        <a href="about.html">About Us</a>
        <a href="contact.html">Contact</a>
    </nav>
    
    <div class="container">
        <h1>About Us</h1>
        <p>Learn more about our company and mission.</p>
        
        <h2>Our Story</h2>
        <p>This website is automatically deployed on an AWS EC2 instance using a shell script.</p>
        
        <h2>Technology Stack</h2>
        <ul>
            <li>AWS EC2 Ubuntu Server</li>
            <li>Apache Web Server</li>
            <li>Git Version Control</li>
            <li>Bash Automation Script</li>
        </ul>
    </div>
</body>
</html>
EOF

    # Create contact page
    cat > "${site_dir}/contact.html" << EOF
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Contact - ${SITE_NAME}</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; }
        nav { background: #333; padding: 10px; }
        nav a { color: white; margin: 0 15px; text-decoration: none; }
        .container { max-width: 800px; margin: 0 auto; }
        form { display: grid; gap: 10px; max-width: 500px; }
        input, textarea { padding: 8px; margin: 5px 0; }
        button { background: #333; color: white; padding: 10px; border: none; }
    </style>
</head>
<body>
    <nav>
        <a href="index.html">Home</a>
        <a href="about.html">About Us</a>
        <a href="contact.html">Contact</a>
    </nav>
    
    <div class="container">
        <h1>Contact Us</h1>
        <p>Get in touch with us through the form below.</p>
        
        <form onsubmit="alert('Thank you for your message! (This is a demo form)'); return false;">
            <label for="name">Name:</label>
            <input type="text" id="name" name="name" required>
            
            <label for="email">Email:</label>
            <input type="email" id="email" name="email" required>
            
            <label for="message">Message:</label>
            <textarea id="message" name="message" rows="4" required></textarea>
            
            <button type="submit">Send Message</button>
        </form>
        
        <h2>Server Contact Information</h2>
        <ul>
            <li>Instance ID: $(curl -s http://169.254.169.254/latest/meta-data/instance-id)</li>
            <li>Availability Zone: $(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone)</li>
            <li>Launch Time: $(date -d @$(curl -s http://169.254.169.254/latest/meta-data/launch-time))</li>
        </ul>
    </div>
</body>
</html>
EOF

    # Set proper permissions
    chown -R www-data:www-data "$site_dir"
    chmod -R 755 "$site_dir"
    
    print_status "$GREEN" "Website structure created successfully"
    return 0
}

# Function to initialize Git repository
initialize_git_repository() {
    print_status "$BLUE" "Initializing Git repository..."
    
    local site_dir="${WEB_ROOT}/${SITE_NAME}"
    
    if [ ! -d "$site_dir" ]; then
        print_status "$RED" "Website directory not found. Cannot initialize Git repository."
        return 1
    fi
    
    cd "$site_dir" || return 1
    
    # Initialize Git repository
    if git init >> "$LOG_FILE" 2>&1; then
        git add . >> "$LOG_FILE" 2>&1
        git commit -m "Initial commit: Static website deployment $(date)" >> "$LOG_FILE" 2>&1
        
        print_status "$GREEN" "Git repository initialized successfully"
        
        # Create a simple deployment script in the repository
        cat > deploy.sh << 'EOF'
#!/bin/bash
# Simple deployment script
echo "Deploying updates..."
git pull origin main
systemctl reload apache2
echo "Deployment completed at $(date)"
EOF
        
        chmod +x deploy.sh
        git add deploy.sh
        git commit -m "Add deployment script" >> "$LOG_FILE" 2>&1
        
        return 0
    else
        print_status "$RED" "Failed to initialize Git repository"
        return 1
    fi
}

# Function to clone from existing Git repository (alternative approach)
clone_git_repository() {
    local repo_url=$1
    
    print_status "$BLUE" "Cloning Git repository: $repo_url"
    
    local site_dir="${WEB_ROOT}/${SITE_NAME}"
    
    if [ -d "$site_dir" ]; then
        print_status "$YELLOW" "Website directory exists. Removing before clone..."
        rm -rf "$site_dir"
    fi
    
    if git clone "$repo_url" "$site_dir" >> "$LOG_FILE" 2>&1; then
        chown -R www-data:www-data "$site_dir"
        chmod -R 755 "$site_dir"
        print_status "$GREEN" "Git repository cloned successfully"
        return 0
    else
        print_status "$YELLOW" "Failed to clone repository. Creating default structure instead."
        create_website_structure
        return 1
    fi
}

# Function to configure firewall
configure_firewall() {
    print_status "$BLUE" "Configuring firewall for web access..."
    
    # Check if ufw is available
    if command -v ufw &> /dev/null; then
        # Enable SSH access
        ufw allow ssh >> "$LOG_FILE" 2>&1
        
        # Enable HTTP access
        ufw allow 'Apache Full' >> "$LOG_FILE" 2>&1
        
        # Enable and start firewall
        echo "y" | ufw enable >> "$LOG_FILE" 2>&1
        
        ufw status >> "$LOG_FILE" 2>&1
        print_status "$GREEN" "Firewall configured successfully"
    else
        print_status "$YELLOW" "UFW not available. Installing..."
        apt-get install ufw -y >> "$LOG_FILE" 2>&1
        configure_firewall
    fi
}

# Function to test website deployment
test_website() {
    print_status "$BLUE" "Testing website deployment..."
    
    # Test Apache service
    if systemctl is-active --quiet apache2; then
        print_status "$GREEN" "Apache service is running"
    else
        print_status "$RED" "Apache service is not running"
        return 1
    fi
    
    # Test local access
    if curl -s http://localhost | grep -q "${SITE_NAME}"; then
        print_status "$GREEN" "Local website access test passed"
    else
        print_status "$RED" "Local website access test failed"
    fi
    
    # Test website files
    local site_dir="${WEB_ROOT}/${SITE_NAME}"
    if [ -f "${site_dir}/index.html" ] && [ -f "${site_dir}/about.html" ] && [ -f "${site_dir}/contact.html" ]; then
        print_status "$GREEN" "All website files are present"
    else
        print_status "$RED" "Missing website files"
    fi
    
    # Display access information
    local public_ip=$(curl -s http://checkip.amazonaws.com)
    print_status "$BLUE" "Website should be accessible at: http://${public_ip}"
    print_status "$BLUE" "Or via localhost: http://localhost"
}

# Function to display deployment summary
display_summary() {
    print_status "$GREEN" "=== DEPLOYMENT SUMMARY ==="
    echo "Website Name: $SITE_NAME"
    echo "Web Root: ${WEB_ROOT}/${SITE_NAME}"
    echo "Apache Status: $(systemctl is-active apache2)"
    echo "Git Version: $(git --version 2>/dev/null | head -n1 || echo 'Not available')"
    echo "Public IP: $(curl -s http://checkip.amazonaws.com)"
    echo "Log File: $LOG_FILE"
    echo "Access URLs:"
    echo "  - http://$(curl -s http://checkip.amazonaws.com)"
    echo "  - http://localhost"
    echo "  - http://$(hostname)"
}

# Main deployment function
main_deployment() {
    log_message "INFO" "Starting EC2 Web Server Deployment"
    
    print_status "$BLUE" "Starting AWS EC2 Web Server Deployment..."
    echo "This script will:"
    echo "1. Update system packages"
    echo "2. Install and configure Apache"
    echo "3. Create static website (Home, About, Contact pages)"
    echo "4. Install and configure Git"
    echo "5. Initialize Git repository"
    echo "6. Configure firewall"
    echo "7. Test deployment"
    echo
    
    # Step 1: Update packages
    if ! update_package_lists; then
        print_status "$RED" "Package update failed. Exiting."
        exit 1
    fi
    
    # Step 2: Install Apache
    if ! install_apache; then
        print_status "$RED" "Apache installation failed. Exiting."
        exit 1
    fi
    
    # Step 3: Configure Apache
    if ! configure_apache; then
        print_status "$RED" "Apache configuration failed. Exiting."
        exit 1
    fi
    
    # Step 4: Install Git
    if ! install_git; then
        print_status "$YELLOW" "Git installation had issues, but continuing..."
    fi
    
    # Step 5: Create website or clone from Git
    if [ -n "$GIT_REPO" ] && [ "$GIT_REPO" != "https://github.com/example/static-website.git" ]; then
        clone_git_repository "$GIT_REPO"
    else
        create_website_structure
    fi
    
    # Step 6: Initialize Git repository
    if ! initialize_git_repository; then
        print_status "$YELLOW" "Git repository initialization had issues, but continuing..."
    fi
    
    # Step 7: Configure firewall
    if ! configure_firewall; then
        print_status "$YELLOW" "Firewall configuration had issues, but continuing..."
    fi
    
    # Step 8: Test deployment
    test_website
    
    # Final summary
    display_summary
    
    log_message "SUCCESS" "EC2 Web Server Deployment completed"
}

# Function to show usage
usage() {
    echo "Usage: $0 [options]"
    echo "Options:"
    echo "  -s, --site-name NAME    Set website name (default: my-website)"
    echo "  -r, --repo URL          Git repository URL to clone"
    echo "  -h, --help              Show this help message"
    echo
    echo "Example: $0 --site-name mycompany --repo https://github.com/mycompany/website.git"
}

# Parse command line arguments
while [[ $# -gt 0 ]]; do
    case $1 in
        -s|--site-name)
            SITE_NAME="$2"
            shift 2
            ;;
        -r|--repo)
            GIT_REPO="$2"
            shift 2
            ;;
        -h|--help)
            usage
            exit 0
            ;;
        *)
            echo "Unknown option: $1"
            usage
            exit 1
            ;;
    esac
done

# Check if running on EC2 (optional)
check_ec2() {
    if curl -s http://169.254.169.254/latest/meta-data/ &> /dev/null; then
        print_status "$GREEN" "Running on AWS EC2 instance"
        return 0
    else
        print_status "$YELLOW" "Not running on EC2 (or metadata service unavailable)"
        return 1
    fi
}

# Main execution
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    # Script is being executed directly
    print_status "$BLUE" "AWS EC2 Web Server Deployment Script"
    echo "Log file: $LOG_FILE"
    echo
    
    # Check if running as root
    if [ "$EUID" -ne 0 ]; then
        print_status "$RED" "Please run as root or with sudo"
        exit 1
    fi
    
    # Check EC2 environment
    check_ec2
    
    # Start deployment
    main_deployment
fi
