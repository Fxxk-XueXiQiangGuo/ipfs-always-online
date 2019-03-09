require 'rake'
require 'date'

USERNAME = "fuck-xuexiqiangguo"
REPO = "Fuck-XueXiQiangGuo"

def check_destination
  if Dir.exist? "repo"
    Dir.chdir("repo") { sh "git pull" }
  else
    sh "git clone https://github.com/#{USERNAME}/#{REPO}.git repo"
  end
end

task :init do

    unless Dir.exist? "#{ENV['HOME']}/.ipfs/keystore"
      sh "ipfs init"
    end

end

task :deploy do

    # Detect pull request
    if ENV['TRAVIS_PULL_REQUEST'].to_s.to_i > 0
      puts 'Pull request detected.'
      exit
    end

    # Make sure destination folder exists as git repo
    check_destination

    sh "ipfs swarm peers"
    ipfs_hash = `ipfs add -r -Q repo`.match(/\w+/)[0]
    sh "ipfs pin add -r /ipfs/#{ipfs_hash}"
    sh "ipfs name publish --key=key #{ipfs_hash}"

end

task :sync, [:minutes] do |t, args|

    args.with_defaults(:minutes => 10)
    minutes = args.minutes.to_i

    max = (minutes - 1) * 60 + 40
    i = 0

    puts "\nsyncing"

    while i < max do

      if i % 30 == 0 then
        peers = `ipfs swarm peers | wc -l`.match(/\d+/)[0].to_i
        puts "Connected to #{peers} peers"  
      end

      sleep(1)
      i += 1

    end
  
end

task :test do

    puts "\ntesting"

    ipns_hash = `ipfs key list -l`.match(/(\w{46}) key/)[1]
    ipfs_hash = `ipfs name resolve #{ipfs_hash}`.match(/\w{46}/)[0]

    gateways = [ "cloudflare-ipfs.com", "ipfs.io", "ipfs.ink" ]

    gateways.each do |i|
      sh "curl 'https://#{i}/ipfs/#{ipfs_hash}' > /dev/null"
      sh "curl 'https://#{i}/ipns/#{ipns_hash}' > /dev/null"
    end

end
