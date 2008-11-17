require "erb"
require "rdiscount"

HOME = ENV["HOME"]
CS3 = HOME + "/Library/Application Support/Adobe/Fireworks CS3/"
CS4 = HOME + "/Library/Application Support/Adobe/Fireworks CS4/"
destinations = [CS3,CS4]

desc "Install commands and extensions in Fireworks CS3 and CS4"
task :install do
  puts "Installing commands..."
  destinations.each do |dest|
    if File.directory?(dest)
      %x(rsync -azv 'Commands' 'en' '#{dest}')
    end
  end
end

desc "Build MXP file with Commands"
task :commands do
# TODO: Install on *user* configuration, dammit
# TODO: Find a way of installing onto 
  MXI = <<-XML
<?xml version="1.0" encoding="UTF-8"?>
<macromedia-extension name="Orange Commands" version="0.9" type="command" requires-restart="true">
  <author name="Ale Muñoz" />
  <products>
    <product name="Fireworks" version="7" primary="true" />
  </products>
  <description>
    <![CDATA[
    An amazingly wonderful collection of Commands for Fireworks :)
    ]]>
  </description>
  <ui-access>
    <![CDATA[
<%= @documentation %>
    ]]>
  </ui-access>
  <files>
<% @files.each do |filename| %>    <file source="<%= filename %>" destination="$fireworks/Configuration/<%= filename %>" />
<% end %>
  </files>
</macromedia-extension>
XML

  @documentation = RDiscount.new(File.read("README.markdown")).to_html.gsub(/^\n/,"")
  @files = Dir["Commands/**/**.jsf","Commands/**/**.js","en/**/**.xml"]
  open("OrangeCommands.mxi","w") do |f|
    f << ERB.new(MXI).result
  end
end

desc "Build XML for keyboard shortcuts"
task :shortcuts do
  # TODO: Complete KEYCODES & MODIFIERS array

  OUTPUT = "en/Keyboard Shortcuts/Shortcuts.xml"

  MODIFIERS = {
    :CTRL => 8,
    :ALT => 4, # Wild guess
    :SHIFT => 2,
    :ALT => 1
  }
  KEYCODES = {
    :kLEFT => "28",
    :kRIGHT => "29",
    :kUP => "30",
    :kDOWN => "31",

    :k0 => "48", 
    :k1 => "49",
    :k2 => "50",
    :k3 => "51",
    :k4 => "52",
    :k5 => "53",
    :k6 => "54",
    :k7 => "55",
    :k8 => "56",
    :k9 => "57",

    :kA => "65",
    :kB => "66",
    :kC => "67",
    :kD => "68",
    :kE => "69",
    :kF => "70",
    :kG => "71",
    :kH => "72",
    :kI => "73",
    :kJ => "74",
    :kK => "75",
    :kL => "76",
    :kM => "77",
    :kN => "78",
    :kO => "79",
    :kP => "80",
    :kQ => "81",
    :kR => "82",
    :kS => "83",
    :kT => "84",
    :kU => "85",
    :kV => "86",
    :kW => "87",
    :kX => "88",
    :kY => "89",
    :kZ => "90",
  }

  class Command
    attr_accessor :name
    attr_accessor :modifier
    attr_accessor :key

    def initialize(command_path,shortcut_line)
      self.name = File.basename(command_path,".jsf")
      modifier = 0
      parts = shortcut_line.match(/shortcut: (.*)/)[1].split(" + ")
      self.key = KEYCODES["k#{parts.pop}".to_sym]
      parts.each do |item|
        if MODIFIERS[item.to_sym]
          modifier += MODIFIERS[item.to_sym]
        end
      end
      self.modifier = modifier
    end
  end

  @commands = []

  Dir["Commands/**/**.jsf"].each do |f|
    File.readlines(f).each do |line|
      if line =~ /shortcut:/
        @commands << Command.new(f,line)
        break
      end
    end
  end

  open(OUTPUT,"w") do |f|
    f << ERB.new(IO.read("Shortcuts.erb")).result
  end

  # %x(mate "#{OUTPUT}")
end