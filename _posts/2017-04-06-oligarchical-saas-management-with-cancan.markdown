---
layout: post
title:  "Oligarchical SaaS Management with CanCan"
date:   2017-04-06 14:34:09 +0000
categories: ruby cancan saas
comments: true
---

Despite moving from [Ryan Bates'](http://www.railscasts.com) curation to a community based maintenance a number of years ago, CanCan ([now known as
CanCanCan](https://github.com/CanCanCommunity/cancancan)) still sees prevalent use in the Rails community and is the leader amongst authorization
gems in monthly downloads.

Therefore, I assume, I am not the first developer to run into the problem of managing a small number of high-value clients with this authorization library.

Let me explain by example.

You've built a simple SaaS application, and, lo and behold, you land your first paying client, the Kingdom of Grimmdor!

They are very happy with the system, under the current ability file, and pay you handsomely every month.

{% highlight ruby %}
 class Ability
  include CanCan::Ability

  def initialize(user)
    return if user.blank?

    case user.role.name
    when "King"
      can :manage, all

    when "Blacksmith"
      can :manage, Anvil
      can :create, Sword
      cannot :wield, Sword
      can [:create, :update], Income do |inc|
        inc.legal_tender?
      end

    when "Knight"
      can :wield, Sword
      cannot :create, Sword
      cannot :manage, Anvil

    when "Merchant"
      cannot :create, Sword
      cannot :wield, Sword
      can :manage, Income do |inc|
        inc.legal_tender?
      end
    end
  end
end
 {% endhighlight %}

Thanks to a very sucessful drip campaign and word of mouth, before you know it, the Kingdom of MaxWoodia approaches you with a sackful of gold asking you to join
your client base.

All is great, until you find an angry note via carrier pigeon that there is a problem. In MaxWoodia, all citizens are reared with swordsmanship from birth and therefore
your blacksmiths and merchants must also be able to wield the weapon. This ability is in direct contravention with Grimmdor's needs.

You resolve that you're not going to throw away perfectly good gold and resolve the issue with the following updated ability file:


{% highlight ruby %}
 class Ability
  include CanCan::Ability

  def initialize(user)
    return if user.blank?

    case user.role.name
    when "King"
      can :manage, all

    when "Blacksmith"
      can :manage, Anvil
      can :create, Sword
      cannot :wield, Sword do |bsmith|
        bsmith.kingdom == "Grimmdor"
      end
      can [:create, :update], Income do |inc|
        inc.legal_tender?
      end

    when "Knight"
      can :wield, Sword
      cannot :create, Sword
      cannot :manage, Anvil

    when "Merchant"
      cannot :create, Sword
      cannot :wield, Sword do |merchant|
        merchant.kingdom == "Grimmdor"
      end
      can :manage, Income do |inc|
        inc.legal_tender?
      end
    end
  end
end
 {% endhighlight %}

Okay, that's not exactly awe inspiring, but cash is king and you're here to make money, right? You're a SaaS-entrepreneur and quickly taking over the world. You think.

A third client approaches you, more excited about your app than the rest and with more money than the other two combined. There's just one catch. The Kingdom of Tenderlovistan
is made up of a collection of super-mutant cats that are able to do anything they want.

To boot, you are having meetings with two other potential clients next week who have already indicated their needs to have slightly different Abilities. How far do you twist your Abilities file before it breaks?

My solution is Oligarchical CanCan.

Requirements: small number of high value SaaS clients that rely on identical systems but for the Abilities file.

Instead of having one monster Ability file, consider spreading it out.

You can turn your ability file into a `case/when` that instantiates different files depending on the client/kingdom.

{% highlight ruby %}
  class Ability
    include CanCan::Ability
    include Ability::Oligarchy

    def initialize(user)
      return if user.blank?

      case user.kingdom
      when "Grimmdor"
        grimmdor_abilities(user)
      when "Maxwoodia"
        maxwoodia_abilities(user)
      when "Tenderlovistan"
        tenderlovistan_abilities(user)
      end
    end
  end
{% endhighlight %}

Then move the ability file where is most useful for you:

{% highlight ruby %}
  #app/models/concerns/abilities/grimmdor_abilities.rb
  module Ability::Oligarchy
    extend ActiveSupport::Concern

    included do
      def grimmdor_abilities(user)
        case user.role.name
        when "King"
          can :manage, all

        when "Blacksmith"
          can :manage, Anvil
          can :create, Sword
          cannot :wield, Sword
          can [:create, :update], Income do |inc|
            inc.legal_tender?
          end

        when "Knight"
          can :wield, Sword
          cannot :create, Sword
          cannot :manage, Anvil

        when "Merchant"
          cannot :create, Sword
          cannot :wield, Sword
          can :manage, Income do |inc|
            inc.legal_tender?
          end
        end
      end
    end
  end
{% endhighlight %}

Extending this out to all clients, you can effectively cater to their special needs without turning your Ability file into something of terror.

FINAL NOTE: For longer term scalability, or for larger Oligarchical setups, I would heartily recommend a specific feature test folder to handle derogations (such as `spec/features/abilities/tenderlovistan_derogations_spec.rb`) to document
and test their unique ability features. It would also be highly recommended to have a default "example_abilities" file that new clients are started off with.
