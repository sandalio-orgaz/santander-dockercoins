FROM ruby
RUN gem install sinatra
COPY hasher.rb .

ENTRYPOINT ["ruby"]
CMD ["hasher.rb"]
#CMD ruby hasher.rb
