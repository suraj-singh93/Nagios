# Nagios

Nagios monitors your entire IT infrastructure to ensure systems, applications, services, and business processes are functioning properly. In the event of a failure, Nagios can alert technical staff of the problem, allowing them to begin remediation processes before outages affect business processes, end-users, or customers.

### sample configuration:


  1. host/host-group
  
          define host {
          use		        linux-server ### Inherit default values from a template/template.cfg (placed at <nagios-home-dir>/etc/)
          host_name	    jenkins ## Name of host
          alias		      jenkins
          address		    192.168.0.105 
          parents		    MAC  ### check Nagios doc
          hostgroups    Linux ## optional
        }

         define hostgroup {

            hostgroup_name          Linux           ; The name of the hostgroup
            alias                   Linux            ; Long name of the group
            members                 localhost,jenkins               ;(Optional) Comma separated list of hosts that belong to          this group
        }
        
  
  2. services/services-group:


          define service {

            use                     local-service           ; Name of service template to use
            hostgroup_name	        Linux 
            service_description     PING
            check_command           check_ping!100.0,20%!500.0,60%  ; ! to specify arguments to commands
          }

          define servicegroup {
            servicegroup_name	        HTTP
            alias			                HTTP
            members    		            localhost,HTTP_80,DOCKER_HOST,HTTP_80,jenkins,HTTP_8080 ; host_name,service,host_name,service.....
          }

  3. Commands:
  
         define command {

            command_name    check_ping
            command_line    $USER1$/check_ping -H $HOSTADDRESS$ -w $ARG1$ -c $ARG2$ -p 5 
          }


  4. Command check_ping execute:
      <nagios-home-dir>/libexec/check_ping -H <IP> -w 100.0,20% -c 500.0,60% -p 5
      output: PING OK - Packet loss = 0%, RTA = 0.65 ms|rta=0.655000ms;100.000000;500.000000;0.000000 pl=0%;20;60;0
      Above output used by nagios server.
  
  
  Note: Place services, commands and hosts file in <nagios-home-dir>/etc/.
        Add below lines in nagios.cfg (<nagios-home-dir>/etc/)
  
             # Definitions for monitoring the  (Linux) host
              cfg_dir=/usr/local/nagios/etc/hosts


              # Definitions for service file
              cfg_dir=/usr/local/nagios/etc/services

              # commands files
              cfg_dir=/usr/local/nagios/etc/commands
              

  
              
