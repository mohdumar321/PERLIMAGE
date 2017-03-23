FROM centos/s2i-base-centos7

# This image provides a Perl 5.24 environment you can use to run your Perl applications.
MAINTAINER SoftwareCollections.org <sclorg@redhat.com>

EXPOSE 8080

# Image metadata
ENV PERL_VERSION=5.24
ENV ORACLE_HOME=/usr/local/instantclient_11_2
ENV LD_LIBRARY_PATH=/usr/local/instantclient_11_2:/opt/rh/rh-perl524/root/usr/lib64:/opt/rh/httpd24/root/usr/lib64
# ENV PERL_LOCAL_LIB_ROOT=$PERL_LOCAL_LIB_ROOT:/opt/app-root/src/perl5
# ENV PERL_MB_OPT=--install_base /opt/app-root/src/perl5
# ENV PERL_MM_OPT=INSTALL_BASE=/opt/app-root/src/perl5
# ENV PERL5LIB=/opt/app-root/src/perl5/lib/perl5:$PERL5LIB
ENV PATH=/opt/rh/rh-perl524/root/usr/local/bin:/opt/rh/rh-perl524/root/usr/bin:/opt/rh/httpd24/root/usr/bin:/opt/rh/httpd24/root/usr/sbin:/opt/app-root/src/bin:/opt/app-root/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:$PATH
LABEL io.k8s.description="Platform for building and running Perl 5.24 applications" \
      io.k8s.display-name="Apache 2.4 with mod_perl/5.24" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,perl,perl524"

#RUN yum install -y libaio
RUN yum install -y perl-CPAN
RUN yum install -y perl-Template-Toolkit
#RUN cd /opt/app-root/src/
#RUN mkdir /opt/app-root/src/lib
#RUN wget http://search.cpan.org/CPAN/authors/id/M/MJ/MJEVANS/DBD-Oracle-1.75_2.tar.gz 
#RUN gunzip DBD-Oracle-1.75_2.tar.gz
#RUN tar -xvf DBD-Oracle-1.75_2.tar

#RUN mkdir /opt/app-root/src/perl5
#RUN perl -MCPAN -e shell
#RUN cpan install DBD::Oracle
#RUN yum install -y sudo
#RUN chmod -R a+rwx /etc/sudoers
#RUN chmod -R a+rwx /etc/
#RUN echo 'user ALL=(ALL)ALL' > /etc/sudoers
#RUN chmod 664 /etc/passwd
#RUN chmod -R a+rwx /etc/passwd-
RUN yum install -y centos-release-scl && \
    yum-config-manager --enable centos-sclo-rh-testing && \
    INSTALL_PKGS="rh-perl524 rh-perl524-perl-devel rh-perl524-perl-DBI rh-perl524-mod_perl rh-perl524-perl-CPAN rh-perl524-perl-App-cpanminus  libaio " && \
#   rpm -Uvh http://spacewalk.redhat.com/yum/1.0/RHEL/5/x86_64/perl-DBD-Oracle-1.23-3.el5.x86_64.rpm
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
yum clean all

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Each language image can have 'contrib' a directory with extra files needed to
# run and build the applications.
COPY ./contrib/ /opt/app-root

# In order to drop the root user, we have to make some directories world
# writeable as OpenShift default security model is to run the container under
# random UID.

# Oracle instantclient
RUN cp /opt/app-root/etc/oracle/instantclient-basic-linux.x64-11.2.0.4.0.zip /usr/local/instantclient-basic-linux.x64-11.2.0.4.0.zip
RUN cp /opt/app-root/etc/oracle/instantclient-sdk-linux.x64-11.2.0.4.0.zip /usr/local/instantclient-sdk-linux.x64-11.2.0.4.0.zip
RUN cp /opt/app-root/etc/oracle/instantclient-sqlplus-linux.x64-11.2.0.4.0.zip /usr/local/instantclient-sqlplus-linux.x64-11.2.0.4.0.zip

RUN yum install -y unzip
#mkdir -p /etc/local /etc/bin
RUN unzip /usr/local/instantclient-basic-linux.x64-11.2.0.4.0.zip -d /usr/local
RUN unzip /usr/local/instantclient-sdk-linux.x64-11.2.0.4.0.zip -d /usr/local
RUN unzip /usr/local/instantclient-sqlplus-linux.x64-11.2.0.4.0.zip -d /usr/local
RUN ln -s /usr/local/instantclient_11_2 /usr/local/instantclient
RUN ln -s /usr/local/instantclient/libclntsh.so.11.1 /usr/local/instantclient/libclntsh.so
RUN ln -s /usr/local/instantclient/sqlplus /usr/bin/sqlplus

#RUN yum install -y perl-DBI
#RUN make install
#RUN yum install -y perl-CPAN
#RUN yum install -y perl-Template-Toolkit
#RUN mkdir /opt/app-root/src/perl5
#RUN cpan> get DBD::Oracle
RUN chmod -R a+rwx /usr/
RUN chmod -R a+rwx /usr/local/
RUN chmod -R a+rwx /usr/share/
#RUN cd /usr/local cpan DBI
RUN mkdir -p /opt/app-root/etc/httpd.d && \
    sed -i -f /opt/app-root/etc/httpdconf.sed /opt/rh/httpd24/root/etc/httpd/conf/httpd.conf  && \
    chmod -R og+rwx /opt/rh/httpd24/root/var/run/httpd /opt/app-root/etc/httpd.d && \
    chown -R 1001:0 /opt/app-root && chmod -R ug+rwx /opt/app-root

USER 1001
#RUN cd /usr/local/instantclient/ yum install -y libaio
# Enable the SCL for all bash scripts.
ENV BASH_ENV=/opt/app-root/etc/scl_enable \
    ENV=/opt/app-root/etc/scl_enable \
    PROMPT_COMMAND=". /opt/app-root/etc/scl_enable"
#ENV ORACLE_HOME=usr/local/instantclient_11_2
#ENV LD_LIBRARY_PATH=/usr/local/instantclient:/opt/rh/rh-perl524/root/usr/lib64:/opt/rh/httpd24/root/usr/lib64:/usr/local:/opt/app-root/src/:/usr/local/instantclient_11_2
#RUN cd /usr/local
RUN mkdir /opt/app-root/src/lib
#RUN cpan install DBD::Oracle PREFIX=~/opt/app-root/src/lib LIB=~/opt/app-root/src/lib
#RUN sudo chmod -R a+rwx /opt/rh/rh-perl524/root/usr/lib64/
#RUN wget http://search.cpan.org/~pythian/DBD-Oracle-1.74/lib/DBD/Oracle.pm
RUN chmod -R a+rwx /opt/app-root/src/
RUN cd /opt/app-root/src/
RUN wget http://search.cpan.org/CPAN/authors/id/M/MJ/MJEVANS/DBD-Oracle-1.75_2.tar.gz
RUN gunzip DBD-Oracle-1.75_2.tar.gz
RUN tar -xvf DBD-Oracle-1.75_2.tar
RUN chmod -R a+rwx /opt/app-root/src/DBD-Oracle-1.75_2
RUN cd /opt/app-root/src/DBD-Oracle-1.75_2 && perl Makefile.PL PREFIX=~/opt/app-root/src/lib LIB=~/opt/app-root/src/lib && make && make install
#RUN cd /opt/app-root/src/DBD-Oraclemake
#RUN make install

# Set the default CMD to print the usage of the language image
CMD $STI_SCRIPTS_PATH/usage
