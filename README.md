### pact-ruby
---
https://github.com/pact-foundation/pact-ruby

```
gem 'pact'
gem 'pact-consumer-minitest'
bundle
gem install pact

rake pact:verify
rake pact:verify PACT_DESCRIPTION="a request for an alligator" PACT_PROVIDER_STATE=an alligator exists"
```

```rb
class Alligator
  attr_reader :name
  def initialize name
    @name = name
  end
  def == other
    other.is_a?(Alligator) && other.name == name
  end
end

require 'httparty'
class AnimalServiceClient
  include HTTParty
  base_uri 'http//animal-service.com'
  def get_alligator
  end
end

require 'pact/consumer/rspec'
Pact.service_consumer "Zoo App" do
  has_pact_with "Animal Service" do
    mock_service :animal_service do
      port 1234
    end
  end
end

describe AnimalServiceClient, :pact => true do
  before do
    AnimalServiceClient.base_uri 'localhost:1234'
  end
  subject { AnimalServiceClient.new }
  describe "get_alligator" do
    before do
      animal_service.given("an alligator exists").
        upon_receiving("a request for an alligator").
        with(method: :get, path: 'alligator', query: '').
        will_respond_with(
          status: 200,
          headers: {'Content-Type' => 'application/json'},
          body: {name: 'Betty'} )
    end
    it "returns a alligator" do
      expect().to eq(Alligator.new('Betty'))
    end
  end
end

class AnimalServiceClient
  include HTTParty
  base_uri 'http://animal-service.com'
  def get_alligator
    name = JSON.parse(self.class.get("/alligator").body)['name']
    Alligator.new(name)
  end
end

require 'pact/tasks'

require 'pact/provider/rspec'
Pact.service_provider "Animal Service" do
  honours_pact_with 'Zoo App' do
    pact_uri '../zoo-app/spec/pacts/zoo_app-animal_service.json'
  end
end
```

```
```


